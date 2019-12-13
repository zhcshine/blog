---
title: python从excel导出数据并批量替换word文档内容
tags:
  - python
url: 26.html
id: 26
comments: false
categories:
  - 办公
  - 运维
date: 2016-11-09 10:07:56
---

### 老外的代码

第一个是操作word，第二个是操作excel

    from win32com.client import constants, Dispatch
    import pythoncom
    wdStory = 6
    class WordDocument(object):
      """
      Some convenience methods for Word documents accessed
      through COM.
      """
    
      def __init__(self, visible=False):
        self.app = Dispatch("Word.Application")
        self.app.Visible = visible
    
      def new(self, filename=None):
        """
        Create a new Word document. If 'filename' specified,
        use the file as a template.
        """
        self.app.Documents.Add(filename)
    
      def open(self, filename):
        """
        Open an existing Word document for editing.
        """
        self.app.Documents.Open(filename)
    
      def save(self, filename=None):
        """
        Save the active document. If 'filename' is given,
        do a Save As.
        """
        if filename:
          self.app.ActiveDocument.SaveAs(filename)
        else:
          self.app.ActiveDocument.Save()
    
      def save_as(self, filename):
        return self.save(filename)
    
      def print_out(self):
        """
        Print the active document.
        """
        self.app.Application.PrintOut()
    
      def close(self):
        """
        Close the active document.
        """
        self.app.ActiveDocument.Close()
    
      def quit(self):
        """
        Quit Word.
        """
        return self.app.Quit()
    
      def find_and_replace(self, find_str, replace_str):
        """
        Find all occurances of 'find_str' and replace with 'replace_str'
        in the active document.
        """
        self.app.Selection.HomeKey(Unit=wdStory)
        find = self.app.Selection.Find
        find.Text = find_str
        while self.app.Selection.Find.Execute():
          self.app.Selection.TypeText(Text=replace_str)
    

    from win32com.client import constants, Dispatch
    import pythoncom
    import os
    borderTop = 3
    borderBottom = 4
    borderLeft = 1
    borderRight = 2
    borderSolid = 1
    borderDashed = 2
    borderDotted = 3
    colorBlack = 1
    directionUp = -4162
    directionDown = -4121
    directionLeft = -4131
    directionRight = -4152
    class ExcelDocument(object):
      """
      Some convenience methods for Excel documents accessed
      through COM.
      """
      def __init__(self, visible=False):
        self.app = Dispatch("Excel.Application")
        self.app.Visible = visible
        self.sheet = 1
      def new(self, filename=None):
        """
        Create a new Excel workbook. If 'filename' specified,
        use the file as a template.
        """
        self.app.Workbooks.Add(filename)
      def open(self, filename):
        """
        Open an existing Excel workbook for editing.
        """
        self.app.Workbooks.Open(filename)
      def set_sheet(self, sheet):
        """
        Set the active worksheet.
        """
        self.sheet = sheet
      def get_range(self, range):
        """
        Get a range object for the specified range or single cell.
        """
        return self.app.ActiveWorkbook.Sheets(self.sheet).Range(range)
      def set_value(self, cell, value=''):
        """
        Set the value of 'cell' to 'value'.
        """
        self.get_range(cell).Value = value
      def get_value(self, cell):
        """
        Get the value of 'cell'.
        """
        value = self.get_range(cell).Value
        if isinstance(value, tuple):
          value = [v[0] for v in value]
        return value
      def set_border(self, range, side, line_style=borderSolid, color=colorBlack):
        """
        Set a border on the specified range of cells or single cell.
        'range' = range of cells or single cell
        'side' = one of borderTop, borderBottom, borderLeft, borderRight
        'line_style' = one of borderSolid, borderDashed, borderDotted, others?
        'color' = one of colorBlack, others?
        """
        range = self.get_range(range).Borders(side)
        range.LineStyle = line_style
        range.Color = color
      def sort(self, range, key_cell):
        """
        Sort the specified 'range' of the activeworksheet by the
        specified 'key_cell'.
        """
        range.Sort(Key1=self.get_range(key_cell), Order1=1, Header=0, OrderCustom=1, MatchCase=False, Orientation=1)
      def hide_row(self, row, hide=True):
        """
        Hide the specified 'row'.
        Specify hide=False to show the row.
        """
        self.get_range('a%s' % row).EntireRow.Hidden = hide
      def hide_column(self, column, hide=True):
        """
        Hide the specified 'column'.
        Specify hide=False to show the column.
        """
        self.get_range('%s1' % column).EntireColumn.Hidden = hide
      def delete_row(self, row, shift=directionUp):
        """
        Delete the entire 'row'.
        """
        self.get_range('a%s' % row).EntireRow.Delete(Shift=shift)
      def delete_column(self, column, shift=directionLeft):
        """
        Delete the entire 'column'.
        """
        self.get_range('%s1' % column).EntireColumn.Delete(Shift=shift)
      def fit_column(self, column):
        """
        Resize the specified 'column' to fit all its contents.
        """
        self.get_range('%s1' % column).EntireColumn.AutoFit()
      def save(self):
        """
        Save the active workbook.
        """
        self.app.ActiveWorkbook.Save()
      def save_as(self, filename, delete_existing=False):
        """
        Save the active workbook as a different filename.
        If 'delete_existing' is specified and the file already
        exists, it will be deleted before saving.
        """
        if delete_existing and os.path.exists(filename):
          os.remove(filename)
        self.app.ActiveWorkbook.SaveAs(filename)
      def print_out(self):
        """
        Print the active workbook.
        """
        self.app.Application.PrintOut()
      def close(self):
        """
        Close the active workbook.
        """
        self.app.ActiveWorkbook.Close()
      def quit(self):
        """
        Quit Excel.
        """
        return self.app.Quit()
    

### 基本使用方法

    # -*- encoding: utf8 -*-
    
    # 配置项
    import os
    import shutil
    import re
    import time
    import win32com
    from win32com.client import constants
    import xlrd
    XlsFile = 'default.xls'                  # 需要的数据
    OldStrs = ['#CatalogNumber#',
               '#ProductName#',
               '#OSpecies#',
               '#Short#',
               '#Organism#',
               '#Fragment#',
               '#PROTEIN#',
               '#Tag#'
               ]
    
    # 确认模板类型
    DefaultFile = raw_input('Want to use ihc.jpg,input \'Y\',or input \'N\':\n')
    if DefaultFile == 'N':
        DocFile = os.path.abspath('default_noihc.doc')
        print DocFile
    elif DefaultFile == 'Y':
        DocFile = os.path.abspath('default.doc')
        print DocFile
    else:
        raise
    filepath = 'D:/Py/CreateAntibodyWord/NewFile'    # 生成文件目录
    
    
    # 获取括号内容的正则表达式
    patt = re.compile(r"\((.*?)\)", re.I | re.X)
    
    # 打开初始化word文档
    w = win32com.client.Dispatch('Word.Application')
    w.Visible = 0
    w.DisplayAlerts = 0
    
    # 从excel表格中导入数据
    
    data = xlrd.open_workbook(XlsFile) 
    table = data.sheets()[0]
    nrows = table.nrows
    
    # 循环表格中的内容
    for i in range(1, nrows):
        try:
            # 获取新的数据
            CatalogNumber = table.row_values(i)[0]
            ProductName = table.row_values(i)[1]
            OrganismSpecies = table.row_values(i)[2]
            Tag = table.row_values(i)[3]
            Fragment = table.row_values(i)[4]
            Short = patt.findall(ProductName)[0]
            # 获取对应蛋白的货号
            ProteinNumber = re.sub(r'^PA', r'RP', CatalogNumber)
            # Ge多物种没有缩写，用General替代
            Organism = patt.findall(OrganismSpecies)
            if len(Organism) > 0:
                Organism = Organism[0]
            else:
                Organism = 'General'
            # 没有氨基酸片段信息的用原始值替代
            Fragment = patt.findall(Fragment)
            if len(Fragment) > 0:
                Fragment = Fragment[0]
            else:
                Fragment = '#Fragment#'
            NewStrs = [CatalogNumber,
                       ProductName,
                       OrganismSpecies,
                       Short,
                       Organism,
                       Fragment,
                       ProteinNumber,
                       Tag
                       ]
    
            # 打开复制的文件
            NewFileName = CatalogNumber + '-e.doc'                                        # 命名新的文件
            shutil.copyfile(DocFile, filepath + '/' + NewFileName)                        # 复制新的文件
            CopyFile = os.path.abspath(filepath + '/' + NewFileName)                      # 新的文件目录
            doc = w.Documents.Open(FileName=CopyFile)                                     # 打开文件
    
            # 文本框处理
            canvas = doc.Shapes[2]
            canvas.TextFrame.TextRange.Find.Execute(OldStrs[3], False, False, False, False, False, True, 1, True, NewStrs[3], 2)
            canvas.TextFrame.TextRange.Find.Execute(OldStrs[4], False, False, False, False, False, True, 1, True, NewStrs[4], 2)
    
            # 正文文字处理
            w.Selection.Find.ClearFormatting()
            w.Selection.Find.Replacement.ClearFormatting()
            for j in range(len(NewStrs)):
                w.Selection.Find.Execute(OldStrs[j], False, False, False, False, False, True, 1, True, NewStrs[j], 2)
    
            # 获取属性信息并修改,参考https://msdn.microsoft.com/en-us/library/microsoft.office.tools.excel.workbook.builtindocumentproperties(v=vs.120).aspx?cs-save-lang=1&cs-lang=vb#code-snippet-1
            doc.BuiltInDocumentProperties('Title').Value = ProductName
            doc.BuiltInDocumentProperties('Subject').Value = 'Instruction manual (Catalog No.' + CatalogNumber + ')'
            doc.BuiltInDocumentProperties('Keywords').Value = ProductName
            doc.BuiltInDocumentProperties('Category').Value = OrganismSpecies
    
            # 保存文件并关闭
            doc.Save()
            doc.Close()
            print CatalogNumber + ' create word success!'
    
        # 捕获异常并写入log文件中
        except Exception, e:
            print CatalogNumber + ' create word error!'
            print e
            errorfile = open(filepath + '/errorlog.txt', 'a')
            try:
                errorfile.write(CatalogNumber + ' create word error! ' + time.strftime('%Y-%m-%d %H:%M:%S') + '\n')
            except Exception, e:
                doc.Close()
                w.Quit()
                raise e
            finally:
                errorfile.close()
    w.Quit()        
    
    

### 替换Word文档属性里的详细信息

            doc.BuiltInDocumentProperties('Title').Value = ProductName
            doc.BuiltInDocumentProperties('Subject').Value = 'Instruction manual (Catalog No.' + CatalogNumber + ')'
            doc.BuiltInDocumentProperties('Keywords').Value = ProductName
            doc.BuiltInDocumentProperties('Category').Value = Ospecies
    

参考[微软官方说明](http://stackoverflow.com/questions/3022898/python-win32com-automating-word-how-to-replace-text-in-a-text-box)

### 替换Word长文本

使用以下语句替换长文本会报错"字符串过长"

    w.Selection.Find.Execute(OldStrs[j], False, False, False, False, False, True, 1, True, NewStrs[j], 2)
    

使用以下语句

    find = w.Selection.Find
    find.Text = '#SEQUENCE#'
    while w.Selection.Find.Execute():
        w.Selection.TypeText(Text=SEQUENCE)
    

### 替换Word文本框内容

参考以下代码 [http://stackoverflow.com/](http://stackoverflow.com/questions/3022898/python-win32com-automating-word-how-to-replace-text-in-a-text-box)

    import win32com.client
    
    word = win32com.client.Dispatch("Word.Application")
    canvas = word.ActiveDocument.Shapes[0]
    for item in canvas.CanvasItems:
        print item.TextFrame.TextRange.Text
    

或者

    find = shp.TextFrame.TextRange.Find
    find.Text = source
    find.Replacement.Text = target
    find.Execute(Replace=1, Forward=True)