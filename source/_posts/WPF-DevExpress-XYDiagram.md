---
title: WPF DevExpress ChartControl使用之XYDiagram
date: 2017-02-11 00:33:55
categories: DevExpress
tags: 
	- DevExpress Chart
	- WPF
description: WPF中使用DevExpress ChartControl控件绘制XYDiagrem折线图。
---
WPF使用Dev和WinForm有许多不同，相对而言，WPF要更简单和炫酷一点，我只做了一点基本的功能，没有仔细的研究，这里只介绍一下WPF Dev ChartControl绘制XYDiagram的基础。
![XYDiagram折线图](http://i1.piimg.com/567571/506953353caf6f08.png)

XYDiagramControl.xaml页面
```
<UserControl
         xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
         xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
         xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
         xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
         xmlns:dxc="http://schemas.devexpress.com/winfx/2008/xaml/charts"
         xmlns:dxmvvm="http://schemas.devexpress.com/winfx/2008/xaml/mvvm"
         x:Class="WpfControl.XYDiagramControl"
         mc:Ignorable="d">
    <Grid>
        <dxc:ChartControl HorizontalAlignment="Left" Margin="0,0,0,0" VerticalAlignment="Top">
            <dxc:XYDiagram2D  LabelsResolveOverlappingMinIndent="2" x:Name="diagram1" EnableAxisXNavigation="True">
                <dxc:XYDiagram2D.DefaultPane>
                    <dxc:Pane Name="pane"/>
                </dxc:XYDiagram2D.DefaultPane>
                <dxc:XYDiagram2D.AxisY>
                    <dxc:AxisY2D GridLinesMinorVisible="False">
                    </dxc:AxisY2D>
                </dxc:XYDiagram2D.AxisY>
                <dxc:XYDiagram2D.SecondaryAxesY>
                </dxc:XYDiagram2D.SecondaryAxesY>
                <dxc:XYDiagram2D.AxisX>
                    <dxc:AxisX2D GridLinesVisible="False"/>
                </dxc:XYDiagram2D.AxisX>
                <dxc:LineSeries2D MarkerVisible="True" LabelsVisibility="True" ArgumentDataMember="Key" ValueDataMember="Value" x:Name="series1">
                    <dxc:LineSeries2D.LineStyle>
                         <dxc:LineStyle Thickness="1" />
                    </dxc:LineSeries2D.LineStyle>
                </dxc:LineSeries2D>
            </dxc:XYDiagram2D>
            <dxc:ChartControl.Legend>
                <dxc:Legend HorizontalPosition="Right"/>
            </dxc:ChartControl.Legend>
            <dxc:ChartControl.ToolTipController>
                <dxc:ChartToolTipController AutoPopDelay="0" InitialDelay="0"/>
            </dxc:ChartControl.ToolTipController>
        </dxc:ChartControl>
    </Grid>
</UserControl>
```

XYDiagramControl.cs页面
```
using System.Collections.Generic;
using System.Windows.Controls;
using DevExpress.Xpf.Charts;
using Dugufeixue.Common;
namespace WpfControl
{
    /// <summary>
    /// UserControl2.xaml 的交互逻辑
    /// </summary>
    public partial class XYDiagramControl : UserControl
    {
        double maxValue = 0;
        private char divider = '_';
        public XYDiagramControl(List<DataEntity> decs, bool isLine, bool isShowLabel, bool isSameY)
        {
            InitializeComponent();
            int index = 0;
            foreach (DataEntity de in decs)
            {
                Series series = null;
                if (!isSameY)
                {
                    //设置Y轴的第二坐标
                    SecondaryAxisY2D SecondaryAxisY = new SecondaryAxisY2D();
                    diagram1.SecondaryAxesY.Add(SecondaryAxisY);
                    //设置第二坐标的标题
                    AxisTitle title = new AxisTitle();
                    title.Content = de.Ytitle;
                    SecondaryAxisY.Title = title;
                    if (isLine)
                    {
                        //新建一个折线图
                        series = new LineSeries2D();
                        //设置series与第二坐标轴关联
                        XYDiagram2D.SetSeriesAxisY((LineSeries2D)series, SecondaryAxisY);
                    }
                    else
                    {
                        //新建一个柱状图
                        series = new BarSideBySideSeries2D();
                        //设置柱状图的Label
                        SeriesLabel label = new SeriesLabel();
                        label.Indent = 20;
                        series.Label = label;
                        if (isShowLabel)
                        {
                            BarSideBySideSeries2D.SetLabelPosition(series.Label, Bar2DLabelPosition.Outside);
                        }
                        XYDiagram2D.SetSeriesAxisY((BarSideBySideSeries2D)series, SecondaryAxisY);
                    }
                }
                else
                {
                    if (isLine)
                    {
                        series = new LineSeries2D();
                    }
                    else
                    {
                        series = new BarSideBySideSeries2D();
                        SeriesLabel label = new SeriesLabel();
                        label.Indent = 20;
                        series.Label = label;
                        if (isShowLabel)
                        {
                            BarSideBySideSeries2D.SetLabelPosition(series.Label, Bar2DLabelPosition.Outside);
                        }
                    }
                }
                series.Name = "series" + index;
                series.DisplayName = de.Displayname;
                //设置series的数据源
                series.DataSource = de.Dic;
                series.LabelsVisibility = isShowLabel;
                series.ArgumentDataMember = "Key";
                series.ValueDataMember = "Value";
                //向XYDiagram中添加series
                diagram1.Series.Add(series);
                index++;
            }
        }
    }
}
```