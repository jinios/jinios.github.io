---
layout: post
title: 커스텀 UICollectionViewCell의 오토레이아웃- Relative to margin옵션  
tags: iOS UICollectionView UICollectionViewCell
categories: iOS
---
> 커스텀 컬렉션뷰 셀을 사용하다가 알게된 `Relative to margin`에 관한것과 삽질기...

### Background
- 아래의 HolidaysCell클래스는 마트지점의 휴뮤일을 표시하는 커스텀 컬렉션뷰 셀
- UIButton을 이용하여 text 주변에 padding을 넣고싶어서 UIButton을 프로퍼티로 갖고있으며, 버튼의 `contentEdgeInsets`속성을 조정하여 padding 효과를 준다.
- 앱 내에 각기 다른 뷰컨트롤러에서 휴무일을 표시해야했기때문에 해당 셀을 재활용하고싶었음
- 이를 이용하기위해 스토리보드에서 똑같은 역할을 하는 두 개의 콜렉션뷰를 만들어서 각각 다른 뷰컨트롤러에 올림
- 하지만 두 콜렉션뷰 셀의 커스텀클래스또한 똑같이 HolidaysCell 클래스로 지정해줌

```swift
class HolidaysCell: UICollectionViewCell {

    @IBOutlet weak var holidayDate: UIButton!

    override func awakeFromNib() {
        super.awakeFromNib()
        holidayDate.contentEdgeInsets = UIEdgeInsets(top: 3, left: 5, bottom: 3, right: 5)
        holidayDate.layer.cornerRadius = 7.0
        holidayDate.clipsToBounds = true
        holidayDate.backgroundColor = UIColor.darkGray
        holidayDate.titleLabel?.textColor = UIColor.white
        holidayDate.isUserInteractionEnabled = false
    }

    func setData(text: String) {
        holidayDate.setTitle(text, for: .normal)
        holidayDate.setAttributedTitle(self.makeTextWithAttributes(of: text), for: .normal)
    }
    // ...    
}

```

## Issue!!
- **하지만 Delegate에서 똑같은 크기로 지정해줘도 다르게 보임... Cell클래스의 컨텐츠 margin때문에 다르게 보이는듯한 느낌!!ㅠㅠ**
- padding값이 내가 원하는것보다 더 크게 들어가서 버튼위의 텍스트 끝부분이 잘려보였다.
- 코드로 아무리 콜렉션뷰의 inset이나 셀의 inset을 바꿔봐도똑같음


## Resolve
- 스토리보드와 뷰 디버거에서 보니 margin이 적용된 autoLayout 선이 차이점임을 발견
- autoLayout설정에서 first와 second item에서 `Relative to margin`옵션을 체크해제함
- margin없이 cell의 콘텐츠가 적용되어 해결!

- `Relative to margin`옵션이 해제된 constraint 라인
  - <img src="{{ site.url }}/assets/img/post_img/2018-09-03-collectionviewCellMargin1.png" width="100%">
- `Relative to margin`옵션이 체크된 constraint 라인. 해당 오토레이아웃 라인 안쪽으로 컨텐츠가 들어가게된다.
  - <img src="{{ site.url }}/assets/img/post_img/2018-09-03-collectionviewCellMargin2.png" width="100%">

<br>
<br>

- **스토리보드의 인스펙터에서 autoLayout의 `Relative to margin` 옵션을 해제하여 해결!**
  - <img src="{{ site.url }}/assets/img/post_img/2018-09-03-collectionviewCellMargin3.png" width="50%">
