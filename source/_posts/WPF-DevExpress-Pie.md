---
title: WPF DevExpress ChartControl使用之PieChart
date: 2017-02-11 00:51:28
categories: DevExpress
tags: 
	- DevExpress Chart
	- WPF
description: WPF中使用DevExpress ChartControl控件绘制Pie饼状图。
---
饼状图要比XYDiagram要简单一点，大体上也是那些东西，没有了X、Y坐标轴，也就没有了第二坐标，要简单一点。
![WPF饼状图](http://i4.buimg.com/567571/cdf282babe7f9ea8.png)
PieChartControl.xaml
```
<UserControl x:Class="WpfControl.PieChartControl"
         xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
         xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
         xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
         xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
         xmlns:dxc="http://schemas.devexpress.com/winfx/2008/xaml/charts"
         mc:Ignorable="d">
    <Grid>
        <dxc:ChartControl x:Name="chart"
                          MouseDown="chart_MouseDown"
                          MouseMove="chart_MouseMove"
                          MouseUp="chart_MouseUp">
            <dxc:ChartControl.Titles>
                <dxc:Title x:Name="title" Content="Title" Dock="Top" HorizontalAlignment="Center" VerticalAlignment="Top"/>
            </dxc:ChartControl.Titles>
            <dxc:SimpleDiagram2D x:Name="diagram2D">
            </dxc:SimpleDiagram2D>
            <dxc:ChartControl.Legend>
                <dxc:Legend HorizontalPosition="Right"/>
            </dxc:ChartControl.Legend>
        </dxc:ChartControl>
    </Grid>
</UserControl>
```

PieChartControl.cs
```
using System;
using System.Collections.Generic;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Animation;
using DevExpress.Xpf.Charts;
using Dugufeixue.Common;
namespace WpfControl
{
    /// <summary>
    /// PieChartControl.xaml 的交互逻辑
    /// </summary>
    public partial class PieChartControl : UserControl
    {
        const int clickDelta = 200;
        DateTime mouseDownTime;
        bool rotate;
        Point startPosition;
        public PieChartControl(DataEntity de, bool isUseColor)
        {
            InitializeComponent();
            LoadPieChart(de, isUseColor);
        }
        public void LoadPieChart(DataEntity de, bool isUseColor)
        {
            Series series= new PieSeries2D();
            //设置饼状图Label样式
            SeriesLabel label = new SeriesLabel();
            label.Indent = 15;
            label.TextPattern = "{A}: {VP:P2}";
            series.Label = label;
            foreach (KeyValuePair<string, double> kvp in de.Dic)
            {
                //这里和XYDiagram不一样，饼状图只能用SeriesPoint添加点来创建Series，没找到其他的好方法
                SeriesPoint point = new SeriesPoint(kvp.Key, kvp.Value);
                if (isUseColor)
                {
                    SolidColorBrush brush;
                    if (kvp.Key.Contains("运行"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0xFF, 0x00));
                    }
                    else if (kvp.Key.Contains("启动"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0xFF, 0x00));
                    }
                    else if (kvp.Key.Contains("正常"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0xFF, 0x00));
                    }
                    else if (kvp.Key.Contains("停止"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xFF, 0x00, 0x00));
                    }
                    else if (kvp.Key.Contains("启炉"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0xFF, 0x00));
                    }
                    else if (kvp.Key.Contains("停炉"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xFF, 0x00, 0x00));
                    }
                    else if (kvp.Key.Contains("开"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0xFF, 0x00));
                    }
                    else if (kvp.Key.Contains("关"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xFF, 0x00, 0x00));
                    }
                    else if (kvp.Key.Contains("故障"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xFF, 0xFF, 0x00));
                    }
                    else if (kvp.Key.Contains("需监管"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0x00, 0xFF));
                    }
                    else if (kvp.Key.Contains("反馈"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0x00, 0x00, 0xFF));
                    }
                    else if (kvp.Key.Contains("高料位报警"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xFF, 0xC0, 0xCB));
                    }
                    else if (kvp.Key.Contains("无状态"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xCC, 0xCC, 0xCC));
                    }
                    else if (kvp.Key.Contains("无"))
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xCC, 0xCC, 0xCC));
                    }
                    else
                    {
                        brush = new SolidColorBrush(Color.FromRgb(0xCC, 0xCC, 0xCC));
                    }
                    //设置每一个扇形的不同颜色
                    point.Brush = brush;
                }
                series.Points.Add(point);
            }
            series.LabelsVisibility = true;
            PieSeries2D.SetLabelPosition(series.Label,PieLabelPosition.Outside);
            //设置Legend的格式
            series.LegendTextPattern = "{A}: {VP:P2}";
            diagram2D.Series.Add(series);
            title.Content = de.Ytitle;
        }
        //设置饼状图的动画效果
        bool IsClick(DateTime mouseUpTime)
        {
            return (mouseUpTime - mouseDownTime).TotalMilliseconds < clickDelta;
        }
        double CalcAngle(Point p1, Point p2)
        {
            Point center = new Point(chart.Diagram.ActualWidth / 2, chart.ActualHeight / 2);
            Point relativeP1 = new Point(p1.X - center.X, p1.Y - center.Y);
            Point relativeP2 = new Point(p2.X - center.X, p2.Y - center.Y);
            double angleP1Radian = Math.Atan2(relativeP1.X, relativeP1.Y);
            double angleP2Radian = Math.Atan2(relativeP2.X, relativeP2.Y);
            double angle = (angleP2Radian - angleP1Radian) * 180 / (Math.PI * 2);
            if (angle > 90)
                angle = 180 - angle;
            else if (angle < -90)
                angle = 180 + angle;
            return angle;
        }
        void chart_MouseUp(object sender, MouseButtonEventArgs e)
        {
            ChartHitInfo hitInfo = chart.CalcHitInfo(e.GetPosition(chart));
            rotate = false;
            if (hitInfo == null || hitInfo.SeriesPoint == null || !IsClick(DateTime.Now))
                return;
            double distance = PieSeries.GetExplodedDistance(hitInfo.SeriesPoint);
            Storyboard storyBoard = new Storyboard();
            DoubleAnimation animation = new DoubleAnimation();
            animation.Duration = new Duration(new TimeSpan(0, 0, 0, 0, 300));
            animation.To = distance > 0 ? 0 : 0.3;
            storyBoard.Children.Add(animation);
            Storyboard.SetTarget(animation, hitInfo.SeriesPoint);
            Storyboard.SetTargetProperty(animation, new PropertyPath(PieSeries.ExplodedDistanceProperty));
            storyBoard.Begin();
        }
        void chart_MouseDown(object sender, MouseButtonEventArgs e)
        {
            mouseDownTime = DateTime.Now;
            Point position = e.GetPosition(chart);
            ChartHitInfo hitInfo = chart.CalcHitInfo(position);
            if (hitInfo != null && hitInfo.SeriesPoint != null)
            {
                rotate = true;
                startPosition = position;
            }
        }
        void chart_MouseMove(object sender, MouseEventArgs e)
        {
            Point position = e.GetPosition(chart);
            ChartHitInfo hitInfo = chart.CalcHitInfo(position);
            if (hitInfo == null)
                return;
            if (rotate && !IsClick(DateTime.Now))
            {
                PieSeries2D series = chart.Diagram.Series[0] as PieSeries2D;
                double angleDelta = CalcAngle(startPosition, position);
                startPosition = position;
            }
        }
    }
}
```