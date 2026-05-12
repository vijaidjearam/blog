---
layout: post
date: 2026-05-12 12:23:00
title: Add Grand Total to Charts from PivotTables
category: excel
tags: excel vba 
---

A **free VBA macro** that automatically adds **Grand Total values** to the **side of each bar** in your **Horizontal Stacked Bar Pivot Chart**. 

Excel Pivot Charts **ignore Grand Totals by design** (to avoid skewing scales), but this script cleverly positions floating, invisible text boxes next to the last segment of each bar (e.g., "online" value), displaying the exact Grand Total (e.g., 425 next to z's bar).

**Perfect for:** Stacked Bar Charts from PivotTables like this example:

| Row Labels | carrefour | grand frais | groceries | online | **Grand Total** |
|------------|-----------|-------------|-----------|--------|-----------------|
| x          | 120       | 0           | 110       | 25     | **255**         |
| y          | 95        | 0           | 0         | 100    | **195**         |
| z          | 0         | 25          | 250       | 150    | **425**         |



## Prerequisites
- **Excel Version:** 2010 or newer (tested on Excel 365, 2019, 2016).
- **Chart Type:** **Horizontal Stacked Bar Chart** (2D only; not 3D).
- **PivotTable Setup:**
  - Grand Totals must be **ON** (PivotTable Design tab > Grand Totals > On for Rows and Columns).
  - Your data should have the Grand Total in the **last column** of the DataBodyRange.
- **Macro Security:** Enable macros (File > Options > Trust Center > Macro Settings > Enable VBA macros).

## Installation (One-Time Setup)
1. Open your Excel workbook with the PivotTable and Chart.
2. Press **ALT + F11** to open the **VBA Editor**.
3. In the left panel, right-click your workbook name > **Insert > Module** (creates `Module1`).
4. **Copy and paste** the full code below into the blank code window.
5. Press **CTRL + S** to save. Close the VBA Editor.

```vba
Sub AddGrandTotalsToSide()
    Dim cht As Chart
    Dim pt As PivotTable
    Dim srs As Series
    Dim ptData As Range
    Dim i As Integer
    Dim shp As Shape
    Dim TotalVal As String
    
    On Error GoTo ErrorHandler
    
    Set cht = ActiveChart
    Set pt = cht.PivotLayout.PivotTable
    
    ' 1. Delete old total textboxes if you run the script again (prevents overlap)
    For i = cht.Shapes.Count To 1 Step -1
        If Left(cht.Shapes(i).Name, 10) = "GrandTotal" Then
            cht.Shapes(i).Delete
        End If
    Next i
    
    ' 2. Target the last series to find the right-most edge of the bar ("online")
    Set srs = cht.SeriesCollection(cht.SeriesCollection.Count)
    Set ptData = pt.DataBodyRange
    
    ' 3. Loop through bars and draw floating text boxes to the SIDE
    For i = 1 To srs.Points.Count
        ' Grab the Grand Total value
        TotalVal = CStr(ptData.Cells(i, ptData.Columns.Count).Value)
        
        ' 4. Calculate position: 5 pixels to the RIGHT of the last bar segment
        Dim boxLeft As Double
        Dim boxTop As Double
        Dim boxWidth As Double
        Dim boxHeight As Double
        
        boxLeft = srs.Points(i).Left + srs.Points(i).Width + 5
        boxTop = srs.Points(i).Top
        boxWidth = 40  ' Width of the textbox
        boxHeight = srs.Points(i).Height
        
        ' Draw the textbox
        Set shp = cht.Shapes.AddTextbox(msoTextOrientationHorizontal, _
            boxLeft, boxTop, boxWidth, boxHeight)
            
        ' 5. Format the textbox so it looks perfectly aligned
        With shp
            .Name = "GrandTotal_" & i
            .TextFrame2.TextRange.Text = TotalVal
            .TextFrame2.TextRange.Font.Bold = True
            .TextFrame2.TextRange.Font.Size = 11
            
            ' Align text to the left so it flows away from the bar
            .TextFrame2.TextRange.ParagraphFormat.Alignment = msoAlignLeft
            ' Center the text vertically so it matches the bar perfectly
            .TextFrame2.VerticalAnchor = msoAnchorMiddle
            
            ' Make the textbox background and outline invisible
            .Fill.Visible = msoFalse
            .Line.Visible = msoFalse
        End With
    Next i
    
    MsgBox "Grand Totals added to the side of the bars!", vbInformation
    Exit Sub

ErrorHandler:
    MsgBox "Error: " & Err.Description
End Sub
```

## Usage (Run Anytime)
1. **Click anywhere on your Pivot Chart** to select it (blue border appears).
2. Press **ALT + F8** to open the Macro dialog.
3. Select **`AddGrandTotalsToSide`** and click **Run**.
4. **Done!** Grand Totals appear instantly to the right of each bar.

**Updates Automatically:**
- If your PivotTable data changes (filters, refresh), **run the macro again**—it auto-deletes old labels and redraws new ones.
- Labels are **dynamic** and perfectly aligned (no manual tweaking needed).

