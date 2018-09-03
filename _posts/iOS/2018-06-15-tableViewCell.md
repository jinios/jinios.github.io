---
layout: post
title: UITableView API문서 번역, 정리(3) - UITableViewCell
tags: iOS UITableView 테이블뷰
categories: iOS
---

> [원문자료 - Table View Programming Guide for iOS ](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/TableView_iPhone/TableViewCells/TableViewCells.html#//apple_ref/doc/uid/TP40007451-CH7-SW1)를 번역, 정리했습니다. 자습용으로 정리한 자료이니 가볍게 참고만해서 보시면 될 것 같고, 틀린 점은 댓글로 알려주시면 감사하겠습니다! :)

## 테이블뷰 셀
- 테이블뷰를 이루는 개별적인 행(row)
- UITableViewCell클래스 상속

## UITableViewCell클래스 셀 콘텐츠의 프로퍼티
- textLabel: (UILabel 타입) 주제목 레이블
- detailTextLabel: (UILabel 타입) 추가 세부 사항 표시를 위한 부제목 레이블
- imageView: (UIImageView 타입) 이미지 표시를 위한 이미지뷰
- UITableViewCell을 커스텀하는 경우, 기본 프로퍼티와 변수명이 같으면 원하는 대로 동작하지 않을 수 있으니 커스텀 셀 클래스의 변수명은 다르게 지어야함.
  - (imageView, textLabel, detailLabel, accessoryView 등의 기본 프로퍼티 이름)

## Configure UITableViewCell
> 아래의 리스트들을 설정하여 셀을 설정할 수 있음. 셀의 요소를 파악하는데 도움이 되어 가져옴.

- `selectionStyle`—Controls the appearance of the cell when selected.
- `accessoryType and accessoryView`—Allow you to set one of the standard accessory views (disclosure indicator or detail disclosure control) or a custom accessory view for a cell in normal (nonediting) mode. For a custom view, you may provide any UIView object, such as a slider, a switch, or a custom view.
- `editingAccessoryType and editingAccessoryView`—Allow you to set one of the standard accessory views (disclosure indicator or detail disclosure control) or a custom accessory view for a cell in editing mode. For a custom view, you may provide any UIView object, such as a slider, a switch, or a custom view.
- `showsReorderControl`—Specifies whether it shows a reordering control when in editing mode. The related but read-only editingStyle property specifies the type of editing control the cell has (if any). The delegate returns the value of the editingStyle property in its implementation of the tableView:editingStyleForRowAtIndexPath: method.
- `backgroundView and selectedBackgroundView`—Provide a background view (when a cell is unselected and selected) to display behind all other views of the cell.
- `indentationLevel and indentationWidth`—Specify the indentation level for cell content and the width of each indentation level.


## 커스텀 셀 만들기

### 셀의 높이: rowHeight프로퍼티
#### 셀 높이 관련 이슈
- 셀 높이를 스토리보드에서 지정해주었지만 셀의 콘텐츠(UIImageview)크기에 따라서 셀 높이가 바뀜
- <img src="{{ site.url }}/assets/img/post_img/2018-06-15-rowHeight2.png" width="50%">
- <img src="{{ site.url }}/assets/img/post_img/2018-06-15-rowHeight1.png" width="60%">
(이미지가 들어간 셀은 80을 넘어서 이미지 height만큼 셀 높이가 늘어남)

#### 1. UIImageview의 Constraint
- **원인:** UIImageview에 왼쪽 이랑 상단, 가로와 세로길이(셀과 동일하도록) constraint밖에안줌
- (보통 view에 frame값 설정해주듯이, 그걸로도 셀 내에서 imageview 위치는 잡힌다고 가정함)
- **시도:** 아래랑 오른쪽위치 까지 사방을 다 막아야 제약을 벗어나지 않을까 하고 하단과 오른쪽에 constraint 0으로 줌

#### 2. rowHeight속성으로 셀 크기 지정하기
- 위에서처럼 스토리보드의 속성인스펙터에서 셀의 높이를 지정해주었지만 그게 다가 아님
- 1. `tableView.rowHeight = 80`처럼 테이블 뷰의 셀의 높이를 지정해줘야함
  - You may set the row height for cells if the delegate doesn’t implement the `tableView(_:heightForRowAt:)` method. The default value of rowHeight is automaticDimension. ([애플문서](https://developer.apple.com/documentation/uikit/uitableview/1614852-rowheight) 참고)
  - 델리게이트 메소드에서 row height을 구현하지 않을거면 위의 방법처럼 프로퍼티에 값을 바로 넣어줘야한다. 만약 rowHeight을 구현하지 않으면 기본값이 automaticDimension이기 때문에 자동 self-resizing이 된다.
- 2. 셀 높이를 델리게이트 메소드를 통해서 지정해주는 방법:  `tableView(_:estimatedHeightForRowAt:) -> CGFloat`
  - 해당 메소드를 구현하지 않으면 **automaticDimension를 리턴** 하여 셀이 self-resizing됨
- 두 방법의 차이점 : **성능** 차이
  - There are performance implications to using `tableView(_:heightForRowAt:)` instead of rowHeight. Every time a table view is displayed, it calls `tableView(_:heightForRowAt:)` on the delegate for each of its rows, which can result in a significant performance problem with table views having a large number of rows. ([애플문서](https://developer.apple.com/documentation/uikit/uitableview/1614852-rowheight) 참고)
  - tableView.rowHeight을 전부 다 세팅해주는 것 보다, 셀이 하나 그려질때마다 델리게이트의 메소드를 호출해서 셀의 높이를 리턴받아서 설정하는것이 더 성능상 비효율을 일으킬 수 있다. 만약 테이블뷰의 셀이 1000개정도 된다면 그 성능 문제는 더욱 심각해질 수 있는 것.


##### 참고자료
- [참고링크 - 애플문서 rowHeight](https://developer.apple.com/documentation/uikit/uitableview/1614852-rowheight)
- [참고링크 - 애플문서 tableView(_:heightForRowAt:)](https://developer.apple.com/documentation/uikit/uitableviewdelegate/1614926-tableview?changes=_6)
- [참고링크 - stackoverflow](https://stackoverflow.com/questions/35169125/minimum-cell-height-with-uitableviewautomaticdimension?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)
- [참고링크 - Table View 유동적인 행 높이 지정하기](http://blog.naver.com/PostView.nhn?blogId=jdub7138&logNo=220963701224&categoryNo=0&parentCategoryNo=115&viewDate=&currentPage=1&postListTopCurrentPage=1&from=search)
- [참고링크 - Raywenderlich(Self-sizing Table View) Cells](https://www.raywenderlich.com/129059/self-sizing-table-view-cells)
