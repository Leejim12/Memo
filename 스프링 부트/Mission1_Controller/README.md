# Controller
내용 : MemberController, MemberVO 사용.
--> List 생성 및 Controller 이용하여 List 내용 변경

## MemberController.java 전체 코드
```
package com.rubypaper.controller;

import java.util.ArrayList;
~~~(임폴트들)

@RestController
public class MemberController{
  private List<MemberVO> memberList;

  public MemberController(){
    System.out.println();

    memberList = new ArrayList<MemberVO>();
    for(int i = 1;i<=20;i++)
  }
}
```