---
title: Python读取PDF文档
date: 2017-10-07 09:09:11
tags: Python
---

使用 pdfminer 或 pdfminer3k 解析 PDF 文档。
<!-- more -->



Python2 安装 [pdfminer](https://pypi.python.org/pypi/pdfminer)
`pip install pdfminer`

Python3 安装 [pdfminer3k](https://pypi.python.org/pypi/pdfminer3k/)
`pip install pdfminer3k`



	```txt
	PDF 文档                            初始化 initialize()
	   |                                        ^
	   V               set_document()           |
	分析器 PDFParser    <============>  文档对象 PDFDocument
	                    set_parser()
	
	资源管理器 PDFResourceManager
	              |
	              V
	    聚合器 PDFPageAggregator ---> 解释器 PDFPageInterpreter
	              |
	              V
	        参数分析器 LAParams
	
	 读取 PDF 文档
	
	 文档对象 PDFDoucment   ------------->  PDF 文档
	         ^               get_pages()
	         |process_page()
	         |
	解释器 PDFPageInterpreter        集合器 PDFPageAggregator
	                                                |
	                                                V
	                                     读取 get_result()
	
	
	w       以写方式打开
	a       以追加模式打开(从 EOF 开始，必要时创建新文件)
	r+      以读写模式打开
	w+      以读写模式打开(参见 w)
	a+      以读写模式打开(参见 a)
	rb      以二进制读模式打开
	wb      以二进制写模式打开(参见 w)
	ab      以二进制追加模式打开(参见 a)
	rb+     以二进制读写模式打开
	wb+     以二进制读写模式打开(参见 w+)
	ab+     以二进制读写模式打开(参见 a+)
	```

## Python3 编码实现

	```py
	# -*- coding: UTF-8 -*-
	from pdfminer.converter import PDFPageAggregator
	from pdfminer.layout import LAParams
	from pdfminer.pdfparser import PDFParser, PDFDocument
	from pdfminer.pdfinterp import PDFResourceManager, PDFPageInterpreter
	from pdfminer.pdfdevice import PDFDevice
	# 获取文档对象
	fp = open('naacl06-shinyama.pdf','rb')#以二进制读的模式打开
	# 创建一个文档关联解释器
	parser = PDFParser(fp)
	# PDF 文档对象
	doc = PDFDocument()
	# 连接解释器和文档对象
	parser.set_document(doc)
	doc.set_parser(parser)
	# 初始化文档
	doc.initialize()
	# 创建 PDF 资源管理器
	resource = PDFResourceManager()
	# 参数分析器
	laparam = LAParams()
	# PDF 一个聚合器
	device = PDFPageAggregator(resource,laparams=laparam)
	# 创建一个页面解释器
	interpreter = PDFPageInterpreter(resource,device)
	# 使用文档对象得到页面的集合
	for page in doc.get_pages():
	    # 使用页面解释器来读取
	    interpreter.process_page(page)
	    # 使用集合器来获得内容
	    layout = device.get_result()
	    for out in layout:
	        if hasattr(out, 'get_text'):
	            print out.get_text()
	```

## Python2.7 编码实现

	```py
	# -*- coding: UTF-8 -*-
	from pdfminer.converter import PDFPageAggregator
	from pdfminer.layout import LAParams
	from pdfminer.pdfparser import PDFParser
	from pdfminer.pdfdocument import PDFDocument
	from pdfminer.pdfpage import PDFPage
	from pdfminer.pdfpage import PDFTextExtractionNotAllowed
	from pdfminer.pdfinterp import PDFResourceManager
	from pdfminer.pdfinterp import PDFPageInterpreter
	# 获取文档对象
	fp = open('naacl06-shinyama.pdf','rb')#以二进制读的模式打开
	# 创建一个文档关联解释器
	parser = PDFParser(fp)
	# PDF 文档对象
	doc = PDFDocument(parser,password='')
	#检查文档是否支持文本导出
	if not doc.is_extractable:
	    raise PDFTextExtractionNotAllowed
	# 创建 PDF 资源管理器
	resource = PDFResourceManager()
	# 参数分析器
	laparam = LAParams()
	# PDF 一个聚合器
	device = PDFPageAggregator(resource,laparams=laparam)
	# 创建一个页面解释器
	interpreter = PDFPageInterpreter(resource,device)
	# 使用文档对象得到页面的集合
	for page in PDFPage.create_pages(doc):
	    # 使用页面解释器来读取
	    interpreter.process_page(page)
	    # 使用集合器来获得内容
	    layout = device.get_result()
	    for out in layout:
	        if hasattr(out, 'get_text'):
	            print out.get_text()
	```