---
title: WinForm DevExpress使用之ChartControl控件绘制图表一——基础
date: 2016-12-07 20:07:46
categories: DevExpress
tags: 
	- DevExpress Chart
	- WinForm
description: WinForm中使用DevExpress ChartControl控件绘制图表基础，包括绘制折线图、柱状图和饼状图。
---
最近因为公司项目需要用到WinForm的DecExpress控件，在这里把一些使用方法总结一下。
DevExpress中有一个专门用来绘制图表的插件ChartControl，可以绘制折线图、饼状图、柱状图等图表。

# 绘制图表基本步骤
<div align=center>
![](http://i2.muimg.com/567571/2149a9d185e05463.png)
</div>
1. 准备数据并绑定
	这里从数据库中取得数据，可以根据自己的需要修改。
```
DataTable dtData = BF<bll_ep_collection_data>.Instance.GetDataTable(sqlwhere, "data_time", parameters);
//建立新的datatable，用来存储XY坐标
DataTable dtXY = new DataTable();
//横坐标的值
dtXY.Columns.Add(new DataColumn("类型"));
var list = new List<object>();
//纵坐标的值
list.Add("数值");
//这里是控制x轴显示数据的数量，ArgumentScaleType类型设置为ScaleType.Qualitative时他不会自动控制x轴的数量
//如果得到数据小于X_COUNT，则x轴显示全部得到的数据
if (X_COUNT < dtData.Rows.Count)
{
    for (int i = 0; i < X_COUNT; i++)
    {
        int index = (dtData.Rows.Count / X_COUNT) * i;
        DataRow item = dtData.Rows[index];
        dtXY.Columns.Add(new DataColumn(item["data_time"].ToString(), typeof(decimal)));
        list.Add(item["value"]);
    }
}
//如果得到数据大于X_COUNT，则x轴X_COUNT条数据
else
{
    for (int i = 0; i < dtData.Rows.Count; i++)
    {
        DataRow item = dtData.Rows[i];
        dtXY.Columns.Add(new DataColumn(item["data_time"].ToString(), typeof(decimal)));
        list.Add(item["value"]);
    }
}
var array = list.ToArray();
dtXY.Rows.Add(array);
this.chartControl1.DataSource = dtXY;
```
	一个更直白的例子：
```
DataTable dt = new DataTable();
dt.Columns.Add(new DataColumn("类型"));
dt.Columns.Add(new DataColumn("2005-1月", typeof(decimal)));
dt.Columns.Add(new DataColumn("2005-2月", typeof(decimal)));
dt.Columns.Add(new DataColumn("2005-3月", typeof(decimal)));
dt.Columns.Add(new DataColumn("2005-4月", typeof(decimal)));
dt.Columns.Add(new DataColumn("2005-5月", typeof(decimal)));
dt.Columns.Add(new DataColumn("2005-6月", typeof(decimal)));
dt.Rows.Add(new object[] { "员工人数", 437, 437, 414, 397, 387, 378 });
dt.Rows.Add(new object[] { "人均月薪", 3964, 3961, 3979, 3974, 3967, 3972 });
dt.Rows.Add(new object[] { "成本TEU", 3104, 1339, 3595.8, 3154.5, 2499.8, 3026 });
dt.Rows.Add(new object[] { "人均生产率", 7.1, 3.06, 8.69, 7.95, 6.46, 8.01 });
dt.Rows.Add(new object[] { "占2005年3月人数比例", 1.06, 1.06, 1, 0.96, 0.93, 0.91 });
```
2. 根据数据创建图形展现
	CreateSeries用于创建一个典型的图形，这里展示创建一条曲线。
```
/// <summary>
/// 根据数据创建一个图形展现
/// </summary>
/// <param name="caption">图形标题</param>
/// <param name="viewType">图形类型</param>
/// <param name="dt">数据DataTable</param>
/// <param name="rowIndex">图形数据的行序号</param>
/// <returns></returns>
private Series CreateSeries(string caption, ViewType viewType, DataTable dt, int rowIndex)
{
    Series series = new Series(caption, viewType);
    for (int i = 1; i < dt.Columns.Count; i++)
    {
        string argument = dt.Columns[i].ColumnName;//参数名称
        decimal value = (decimal)dt.Rows[rowIndex][i];//参数值
        series.Points.Add(new SeriesPoint(argument, value));
    }
    //必须设置ArgumentScaleType的类型，否则显示会转换为日期格式，导致不是希望的格式显示
    //也就是说，显示字符串的参数，必须设置类型为ScaleType.Qualitative
    series.ArgumentScaleType = ScaleType.Qualitative;
    //series.ArgumentScaleType = ScaleType.DateTime;
    series.LabelsVisibility = DevExpress.Utils.DefaultBoolean.False;//显示标注标签
    return series;
}
```
3. 根据图形对象创建一个图表并绑定到CharControl中
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
}
```
4. 调用函数绘制图表
```
//清空ChartControl控件
chartControl1.Series.Clear();
//创建图形对象的列表
List<Series> list = new List<Series>();
foreach (DataRowView item in listBox.Items)
{
    string str = item["key"].ToString();
    //通过LoadData返回一个DataTable
    DataTable dtXY = LoadData(str);
    //创建一个图形对象
    Series series = CreateSeries(caption, type, dtXY, 0);;
    list.Add(series);
}
//根据图形对象列表创建一个图表
CreateChart(chartControl1, list);
```
	小结：在绘制折线图的时候我遇到的一个很大的问题就是没办法控制数据的显示数量，因为数据库中存储的数据量过大，在一定时间段内的数据都能达到上千条，所以不可能一次性全部显示。我想到的解决办法是固定显示多少条数据，比如说20条，然后根据数据量每隔一段距离取一条数据显示。

# 柱状图
<div align=center>
![](http://i1.piimg.com/567571/b144e515659bd454.png)
</div>
绘制柱状图其实和绘制折线图没有什么区别，就是将new Series时的图形类型改为ViewType.Bar即可，这里我想总结一下一个困扰我很久的问题，如何绘制上图所示的一个横坐标对应两个或者多个柱子。
1. 准备数据
```
DataTable dtData = SqlHelper.GetDataSet(sql, parameters).Tables[0];
//建立新的datatable，用来存储XY坐标
DataTable dtXY_max = new DataTable();
DataTable dtXY_min = new DataTable();
dtXY_max.Columns.Add(new DataColumn("类型"));
dtXY_min.Columns.Add(new DataColumn("类型"));
var list_max = new List<object>();
list_max.Add("数值");
var list_min = new List<object>();
list_min.Add("数值");
for (int i = 0; i < dtData.Rows.Count; i++)
{
    DataRow item = dtData.Rows[i];
    string value = item["max"].ToString() + "|" + item["min"].ToString();
    //dtXY.Columns.Add(new DataColumn(item["data_time"].ToString(), typeof(string)));
    //list.Add(value);
    dtXY_max.Columns.Add(new DataColumn(item["data_time"].ToString(), typeof(decimal)));
    list_max.Add(item["max"]);
    dtXY_min.Columns.Add(new DataColumn(item["data_time"].ToString(), typeof(decimal)));
    list_min.Add(item["min"]);
}
var array_max = list_max.ToArray();
dtXY_max.Rows.Add(array_max);
var array_min = list_min.ToArray();
dtXY_min.Rows.Add(array_min);
```
2. 创建图形展现对象方法
```
/// <summary>
/// 根据数据创建一个图形展现
/// </summary>
/// <param name="caption">图形标题</param>
/// <param name="viewType">图形类型</param>
/// <param name="dt">数据DataTable</param>
/// <param name="rowIndex">图形数据的行序号</param>
/// <returns></returns>
private Series CreateSeries(string caption, ViewType viewType, DataTable dt, int rowIndex)
{
    Series series = new Series(caption, viewType);
    for (int i = 1; i < dt.Columns.Count; i++)
    {
        string argument = dt.Columns[i].ColumnName;//参数名称
        decimal value = (decimal) dt.Rows[rowIndex][i];           
        series.Points.Add(new SeriesPoint(argument, value));
    }
    //柱状图柱子的宽度设置
    //BarSeriesView bsv = (BarSeriesView)series.View;
    //bsv.BarWidth = 0.1;
    //必须设置ArgumentScaleType的类型，否则显示会转换为日期格式，导致不是希望的格式显示
    //也就是说，显示字符串的参数，必须设置类型为ScaleType.Qualitative
    series.ArgumentScaleType = ScaleType.Qualitative;
    //series.ArgumentScaleType = ScaleType.DateTime;
    series.LabelsVisibility = DevExpress.Utils.DefaultBoolean.True;//显示标注标签
    return series;
}
```
3. 根据图形对象创建图表
```
/// <summary>
/// 根据图形对象创建一个图表
/// </summary>
/// <param name="chartControl">图标绑定控件</param>
/// <param name="list">图表中的图形展现</param>
/// <returns></returns>
private void CreateChart(ChartControl chartControl, List<Series> series)
{
    chartControl.Series.AddRange(series.ToArray());
    //chartControl.Series.Add(series);
    chartControl.Legend.Visible = true;
    chartControl.SeriesTemplate.LabelsVisibility = DefaultBoolean.True;
    XYDiagram xydiagram = (XYDiagram)chartControl.Diagram;
    xydiagram.AxisX.MinorCount = 10;
}
```

# 饼状图
<div align=center>
![](http://i1.piimg.com/567571/9e32250685b304fd.png)
</div>
1. 准备数据
```
private DataTable CreateChartData()
{
    DataTable dtData = SqlHelper.GetDataSet(sql, parameters).Tables[0];
    DataTable table = new DataTable("Table1");
    table.Columns.Add("Name", typeof(String));
    table.Columns.Add("Value", typeof(Double));
    foreach (DataRow item in dtData.Rows)
    {
        var array = new object[] { item["value_num"], item["count"] };
        table.Rows.Add(array);
    }
    return table;
}
```
2. 创建一个饼状图
```
/// <summary>
/// 根据数据创建一个饼状图
/// </summary>
/// <returns></returns>
private void BuilderDevChart()
{
    Series _pieSeries = new Series("测试", ViewType.Pie);
    _pieSeries.ValueDataMembers[0] = "Value";
    _pieSeries.ArgumentDataMember = "Name";
    _pieSeries.DataSource = CreateChartData();
    chartControl1.Series.Add(_pieSeries);
    _pieSeries.SetPiePercentage();
    _pieSeries.LegendPointOptions.PointView = PointView.ArgumentAndValues;
}
```
3. 设置饼状图显示方式（数值/百分比）
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