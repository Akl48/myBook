[TOC]

### XML 

和XML都是用来表示数据的一种数据格式，JSON更加轻量级 

以标签对成对出现 

#### XML文档部分 

1. 文档声明 
  
   1. 说明文档的字符编码版本等 
2. 元素 
   1. 一个元素包括了开始标签和结束标签 
      1. < video >没有内容简写 
		2. < video >哈哈< video > 
	 2. 一个元素可以包括多个元素 
   3. 最多只有一个根元素 
3. 属性 
   1. 一个元素可以有多个属性 
      1. < video name="java1"> name就是属性要用""括起来 
#### XML解析 
1. DOM 
   1. 一次性将XML文件加载到内存中(适合较小文件) 
2. SAX 
   1. 从根元素开始，一个元素一个元素向下解析(适合大文件) 
##### 苹果原生（NSXMLParser） 
SAX方式解析，使用简单 
```objc 
// 声明XML解析器 
NSXMLParser *parser = [[NSXMLParser alloc]initWithData:data]; 

// 声明代理 
parser.delegate = self; 

// 开始解析（本身是阻塞式的） 
[parser parse]; 
```
代理方法 
```objc 
#pragma --XMLdelegate 
- (void)parserDidStartDocument:(NSXMLParser *)parser{ 
// 开始解析XML的时候调用 
} 

- (void)parserDidEndDocument:(NSXMLParser *)parser{ 
// 结束解析XML的时候调用 
} 

- (void)parser:(NSXMLParser *)parser didStartElement:(nonnull NSString *)elementName namespaceURI:(nullable NSString *)namespaceURI qualifiedName:(nullable NSString *)qName attributes:(nonnull NSDictionary<NSString *,NSString *> *)attributeDict{ 
// 判断是否为空(根元素没有属性) 
// 开始解析某个元素的时候调用 
	NSLog(@"元素名称=%@\n元素内容=%@",elementName,attributeDict); 
// 字典转模型+保存到模型数组中 
} 

- (void)parser:(NSXMLParser *)parser didEndElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName{ 
// 结束解析某个元素的时候调用 
} 
```

###### 框架解析 

lixml2：纯C语言解析（不推荐） 

GDataXML：google公司开发，使用DOM方式解析 