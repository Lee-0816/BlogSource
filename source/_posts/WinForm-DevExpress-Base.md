---
title: WinForm DevExpress使用之ChartControl控件绘制图表二——进阶
date: 2017-02-11 00:02:13
categories: DevExpress
tags: 
	- DevExpress Chart
	- WinForm
description: WinForm中使用DevExpress ChartControl控件绘制图表进阶，包括多坐标折线图，以及柱状图和饼状图的样式设置。
---
# 1. 多坐标折线图
![多坐标折线图](http://i.imgur.com/9QU9FDT.png)
在这个项目中，我需要做不同采集地方和不同数据类型的数据对比，自然而然就用到了多重坐标轴，多重坐标轴可以是多个X轴，也可以是Y轴，它们的处理方式类似。本文通过项目中的实际例子介绍多重Y轴的形式，希望给大家有一个很好的参考。ChartControl图表控件提供了SecondaryAxisY对象来处理多重坐标的问题。CreateAxisY用来创建一个多重坐标轴的代码如下所示，注意这里多重坐标，使用了和Series一样的View.Color颜色，这样方便区分。
```
/// <summary>
/// 创建图表的第二坐标系
/// </summary>
/// <param name="series">Series对象</param>
/// <returns></returns>
private SecondaryAxisY CreateAxisY(Series series)
{
    SecondaryAxisY myAxis = new SecondaryAxisY(series.Name);
    ((XYDiagram)chartControl1.Diagram).SecondaryAxesY.Add(myAxis);
    //判断图形对象是柱状图还是折线图
    string type = series.View.GetType().ToString();
    if (type == "DevExpress.XtraCharts.SideBySideBarSeriesView")
    {
        ((BarSeriesView)series.View).AxisY = myAxis;
    }
    else
    {
        ((LineSeriesView)series.View).AxisY = myAxis;
    }
    myAxis.Title.Text = series.Name;
    myAxis.Title.Alignment = StringAlignment.Far; //顶部对齐
    myAxis.Title.Visible = true; //显示标题
    myAxis.Title.Font = new Font("宋体", 9.0f);
    Color color = series.View.Color;//设置坐标的颜色和图表线条颜色一致
    myAxis.Title.TextColor = color;
    myAxis.Label.TextColor = color;
    myAxis.Color = color;
    return myAxis;
}
```

根据图形对象创建图表时调用CreateAxisY方法
```
/// <summary>
/// 根据图形对象创建一个图表
/// </summary>
/// <param name="chartControl">图表绑定控件</param>
/// <param name="list">图表中的图形展现</param>
/// <returns></returns>
private void CreateChart(ChartControl chartControl, List<Series> list)
{
    chartControl.Series.AddRange(list.ToArray());
    chartControl.Legend.Visible = false;
    chartControl.SeriesTemplate.LabelsVisibility = DefaultBoolean.False;
    //XYDiagram diagram = (XYDiagram)chartControl1.Diagram;
    //diagram.AxisX.DateTimeMeasureUnit = DateTimeMeasurementUnit.Second;
    //diagram.AxisX.DateTimeOptions.Format = DateTimeFormat.Custom;
    //diagram.AxisX.DateTimeOptions.FormatString = "yyyy-MM-dd HH:mm:ss";
    /*------------------------------------新增--------------------------------*/
    //删除之前的第二坐标系，如果不删除会一直重复添加
    ((XYDiagram)chartControl1.Diagram).SecondaryAxesY.Clear();
    for (int i = 0; i < list.Count; i++)
    {
        list[i].View.Color = Color.FromArgb(rand.Next(0, 255), rand.Next(0, 255), rand.Next(0, 255));
        CreateAxisY(list[i]);
    }
}
```

# 2. 折线图，柱状图设置缩放与滚动，并设置每页显示条数
```
//设置滚动条
int cnt = 20;
DevExpress.XtraCharts.XYDiagram xyDiagram1 = (XYDiagram)chartControl1.Diagram;
xyDiagram1.AxisX.Range.Auto = false; //要开启滚动条必须将其设置为false
xyDiagram1.AxisX.Range.MaxValueInternal = 30.5D > (cnt + 1) ? (cnt + 1) : 30.5D;//在不拉到滚动条的时候，X轴显示多个值，既固定的X轴长度。
xyDiagram1.AxisX.Range.MinValueInternal = -0.5D;
xyDiagram1.AxisX.Range.ScrollingRange.Auto = false;
xyDiagram1.AxisX.Range.ScrollingRange.MaxValueSerializable = (cnt + 1).ToString();//整个X轴最多显示多多少个值
xyDiagram1.AxisX.Range.ScrollingRange.MinValueSerializable = "0";
xyDiagram1.AxisX.Range.ScrollingRange.SideMarginsEnabled = true;//是否从X轴原点开始显示
xyDiagram1.AxisX.Range.SideMarginsEnabled = false;
xyDiagram1.AxisX.VisibleInPanesSerializable = "-1";
xyDiagram1.EnableAxisXScrolling = true;//启用滚动条
```

# 3. 柱状图设置Label样式
```
BarSeriesLabel label = (BarSeriesLabel)series.Label;
label.Position = BarSeriesLabelPosition.Top;
label.TextPattern = "{A}: {VP:P2}";
```

# 4. 饼状图设置显示样式
DevExpress设置显示图表数据的样式是使用TextPattern来实现的，比如Label以及Legend等都是通过TextPattern或Pattern来设置的。
```
//设置Legend的样式
_pieSeries.LegendPointOptions.PointView = PointView.ArgumentAndValues;
_pieSeries.LegendPointOptions.Pattern = "{A}: {VP:P2}";
//设置Label的样式
label.Position = PieSeriesLabelPosition.Inside;
label.TextPattern = "{A}: {VP:P2}";
```

# 5. 设置饼状图数据显示方式（数值/百分比）
```
public static class ExtensionClass
{
/// <summary>
/// 设置饼状Series显示方式（值/百分比）
/// </summary>
/// <param name="series">Series</param>
public static void SetPiePercentage(this Series series)
{
	if (series.View is PieSeriesView)
	{
		//设置为值
		//((PiePointOptions)series.PointOptions).PercentOptions.ValueAsPercent = false;
		//((PiePointOptions)series.PointOptions).ValueNumericOptions.Format = NumericFormat.Number;
		//((PiePointOptions)series.PointOptions).ValueNumericOptions.Precision = 0;
		//设置为百分比
		((PiePointOptions)series.PointOptions).PercentOptions.ValueAsPercent = true;
		((PiePointOptions)series.PointOptions).ValueNumericOptions.Format = NumericFormat.Percent;
		((PiePointOptions)series.PointOptions).ValueNumericOptions.Precision = 0;
		}
	}
}
```