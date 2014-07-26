---
layout: post
title: "基于MFC&OpenCV实现的图像读取拖拽的程序"
description: ""
category: MFC
tags: [MFC]
---

---
### 基于MFC&OpenCV实现的图像读取拖拽的程序
---
> 由于换了不熟悉的键盘布局(Dvorak)打字速度太慢了让我都不想用电脑了..             

> 最近小组需要对软件的显示部分做一些改进，于是折腾了几天的MFC查遍各种资料终于达到要求……然后自己又单独实现了一遍，以后也许可以增加功能  

----
#### 实现的功能
1.可以根据窗口的大小调整图像比例，保证图像不失真(类似OpenGL中的正交图)
2.可以通过鼠标拖拽放大图片
3.可以利用OpenCV

----

####调整图像比例，保证图像不失真

> a.读取显示区域矩形大小，选取较短的边(宽或高)同图像相同的边比较，根据这个比率将另一条边缩放
> b.利用MFC的双缓冲机制解决闪屏问题
> c.显示利用了OpenCV的CvvImage类

OnDraw函数如下：
{% highlight java linenos %}
void CPicViewerView::OnDraw(CDC* pDC)
{
	CPicViewerDoc* pDoc = GetDocument();
	ASSERT_VALID(pDoc);
	if (pDoc->GetReady())
	{
		CDC memDC;      //内存绘图设备
		CBitmap memBitmap;    //内存绘图;
		CBitmap* pOldBmp = NULL;
		CRect rect;
		GetClientRect(&rect);
		memDC.CreateCompatibleDC(pDC);
		memBitmap.CreateCompatibleBitmap(pDC,rect.right,rect.bottom);
		pOldBmp = memDC.SelectObject(&memBitmap);
		CBrush bkBrush( RGB(58,110,165) );
		memDC.FillRect( &rect, &bkBrush );
		const IplImage* t_Image = pDoc->GetImage();
		int imageHeight = t_Image->height;
 		int imageWidth = t_Image->width;
 		if (m_oldInflate==-1) //如果显示rect发生变化,重新调整缩放率
  		{ 		
			if (rect.Width()>rect.Height())	
				m_inflate = 0.9*rect.Height()/imageHeight;
			else
				m_inflate = 0.9*rect.Width()/imageWidth;
			m_oldInflate = m_inflate;
			CvSize size; //根据缩放率计算目标图像大小
			size.height=m_inflate*imageHeight;
			size.width=m_inflate*imageWidth;
			IplImage *Image=cvCreateImage(size,IPL_DEPTH_8U,3);
			cvResize(t_Image,Image,CV_INTER_CUBIC);
			CvvImage cimg;
			cimg.CopyOf(Image);
			//调整显示范围使其居中
			CRect memShowRect;
			memShowRect.left=rect.left+(rect.Width()-size.width)/2;
			memShowRect.top=rect.top+(rect.Height()-size.height)/2;
			memShowRect.right=rect.right;
			memShowRect.bottom=rect.bottom;
			m_memShowRect=memShowRect;
			//memShowRect=rect;
			cimg.Show(memDC.GetSafeHdc(),memShowRect.left,memShowRect.top,memShowRect.right,memShowRect.bottom);
			cvReleaseImage(&Image);
		}else{
				m_oldInflate=m_inflate;
				CvSize size; //根据缩放率计算目标图像大小
				size.height=m_inflate*imageHeight;
				size.width=m_inflate*imageWidth;
				IplImage *Image=cvCreateImage(size,IPL_DEPTH_8U,3);
				cvResize(t_Image,Image,CV_INTER_CUBIC);
				CvvImage cimg;
				cimg.CopyOf(Image);
				cimg.Show(memDC.GetSafeHdc(),m_memShowRect.left,m_memShowRect.top,size.width,size.height);
				cvReleaseImage(&Image);
		}
			char str[25];
			sprintf(str, "当前的缩放率为%f" ,m_inflate);
			memDC.TextOut(rect.right-200,rect.bottom-20,str);
			pDC->BitBlt(rect.left,rect.top,rect.right,rect.bottom,&memDC,0,0,SRCCOPY);//将内存中画好的图显示在屏幕上
			memDC.SelectObject(pOldBmp);
			memDC.DeleteDC();						
			memBitmap.DeleteObject();	
	}
{% endhighlight %}
----
#### 鼠标消息的处理

这部分参考了csdn上的一些做法，值得注意的是本程序在显示初始化时根据图像的大小适配了一个内存显示区域，因此在拖拽时只需要改变这个内存显示区域的相对位置.
另一个需要注意的是 CvvImage::Show(HDC dc, int x, int y, int w, int h, int from_x, int from_y) 这个函数，其中 x,y表示在dc中的位置. w，h表示需要绘制图像的宽高. from_x表示从图像的第from_x列开始显示。  from_y表示从图像的第from_y行开始显示。
鼠标消息的处理函数：
{% highlight java linenos %}
void CPicViewerView::OnLButtonDown(UINT nFlags, CPoint point)
{
    if(m_memShowRect.PtInRect(point))
    {
		//::SetCursor(LoadCursor());
        m_bIsPick    = TRUE;
        m_ptPre        = point;
        SetCapture();
    }
	
    CView::OnLButtonDown(nFlags, point);
}

void CPicViewerView::OnLButtonUp(UINT nFlags, CPoint point)
{
    m_bIsPick = FALSE;
    ReleaseCapture();
	
    CView::OnLButtonUp(nFlags, point);
}

void CPicViewerView::OnMouseMove(UINT nflags,CPoint point)
{
    if(m_bIsPick)
    {
        m_memShowRect.top+= point.y-m_ptPre.y;
        m_memShowRect.bottom+= point.y-m_ptPre.y;
        m_memShowRect.right+= point.x-m_ptPre.x;
        m_memShowRect.left+= point.x-m_ptPre.x;
		
        m_ptPre=point;
        Invalidate(FALSE);
    }
}
{% endhighlight %}
----
接下来可以增加些高级功能： repo url：
[Fork](https://github.com/SureD/PictureViewer)