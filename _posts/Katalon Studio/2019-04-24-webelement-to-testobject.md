---
layout: post
cover: assets/images/cover/katalonlogo.png 
title:  "WebElement to TestObject"
navigation: True
tags: [Katalon Studio]
class: post-template
subtitle: "카탈론에서 WebElement를 TestObject로 바꾸는 방법"
background: assets/images/cover/katalonlogo.png 
subclass: 'post tag-getting-started'
author: mem
---

## Problem

How to convert WebElement to TestObject in Katalon Studio?


## Solution


table안에 있는 내용을 갖고 오고 싶다. xpath를 tr 단위의 넓은 범위로 잡고 해당되는 element를 List로 나열한다.

`List<WebElement> CBList = WebUI.findWebElements(findTestObject('Object Repository/Page_Admin_RecruitNotice_CodeManage/checkbox_list_all'), 2)` 

> **xpath를 넓은 범위로 잡는다?** 이게 무슨 말?
>
> 테이블 에서 첫번째 raw의 xpath는 다음과 같다. <br>
> `//table[@id='modalTable']/tbody/tr[1]/td/label/input[@class='checkbox']`
>
> tr의 index를 제외하면 해당 테이블의 모든 raw의 xpath를 가져올 수 있다. <br>
> `//table[@id='modalTable']/tbody/tr/td/label/input[@class='checkbox']`

이렇게하면 문제가 뭐냐..
카탈론은 WebUI로 액션을 주고 싶을 때 TestObject 객체를 파라미터로 넘겨야하는데 저렇게 가져온 리스트의 데이터타입은 WebElement다.
내부적으로 변환하는 메소드는 없고 만들어줘야한다.

```groovy
    //새로운 TestObject를 만들어서 xpath를 추가하는 방법
	def TestObject fromElement(RemoteWebElement element) {
		TestObject testObject = new TestObject();
		testObject.addProperty("xpath", ConditionType.EQUALS, getXPathFromElement(element));
		return testObject;
	}
```

## Side Problem
위와 같은 방법으로 WebElement list를 만들었을 때 위에서부터 순서대로 생성될 것 이라고 생각했다.

> tr[1], tr[2], tr[3], ... 

그런데 xpath의 범위가 넓기 때문에 랜덤 가져와 list로 만들기 때문에 결론적으로 순서대로 안 들어간다....

> tr[2], tr[10], tr[1], ...

내가 하고 싶었던 것은 table의 마지막 인덱스에 해당하는 checkbox를 클릭하고 싶은거엿는데.. 아 ,,

~~xpath를 직접 작성하여 가져오는 일은 하고싶지 않았는데.. A ㅏ .. 방법을 생각해 보겠음 .. ~~

 = > [풀엇다 풀엇다 풀엇다 풀엇다](2019-04-25-dynamic-testobject.md)


*추가 
카탈론 내부의 디폴트 커프텀 키워드에 HTMl 테이블을 핸들링하는 키워드가 있음...

```groovy
	/**
	 * Get all rows of HTML table
	 * @param table Katalon test object represent for HTML table
	 * @param outerTagName outer tag name of TR tag, usually is TBODY
	 * @return All rows inside HTML table
	 */
	@Keyword
	def List<WebElement> getHtmlTableRows(TestObject table, String outerTagName) {
		WebElement mailList = WebUiBuiltInKeywords.findWebElement(table)
		List<WebElement> selectedRows = mailList.findElements(By.xpath("./" + outerTagName + "/tr"))
		return selectedRows
	}
```