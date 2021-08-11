---
layout: post
title: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 5. 회원 관리 예제 - 웹 MVC 개발
tag: [Spring, SpringEntry, UF]
permalink: /docs/Spring/Entry/5
---
# 회원 관리 예제 - 웹 MVC 개발

## 회원 웹 기능 - 홈 화면 추가

**홈 컨트롤러 추가**

![회원 웹 기능 - 홈 화면 추가 01](https://user-images.githubusercontent.com/52024566/123198524-37498b80-d4e8-11eb-8b76-3d739409be8f.png)

**회원 관리용 홈**

![회원 웹 기능 - 홈 화면 추가 02](https://user-images.githubusercontent.com/52024566/123198526-37498b80-d4e8-11eb-9586-e59c350953c9.png)

> 참고 : 컨트롤러가 정적 파일보다 우선순위가 높다.

### 완성 예제

![회원 웹 기능 - 홈 화면 추가 03](https://user-images.githubusercontent.com/52024566/123198527-37e22200-d4e8-11eb-9fe3-ec850fcf060d.png)

## 회원 웹 기능 - 등록

### 회원 등록 폼 개발

**회원 등록 폼 컨트롤러**

```java
@Controller
public class MemberController {
    
	private final MemberService memberService;
    
	@Autowired
	public MemberController(MemberService memberService) {
		this.memberService = memberService;
	}
    
	@GetMapping(value = "/members/new")
	public String createForm() {
		return "members/createMemberForm";
	}
    
}
```

**회원 등록 폼 HTML** (`resources/templates/members/createMemberFrom`)

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    
<div class="container">
	<form action="/members/new" method="post">
		<div class="form-group">
			<label for="name">이름</label>
			<input type="text" id="name" name="name" placeholder="이름을 입력하세요">
		</div>
		<button type="submit">등록</button>
	</form>

</div> <!-- /container -->
    
</body>
</html>
```

### 회원 등록 컨트롤러

**웹 등록 화면에서 데이터를 전달 받을 폼 객체**

```java
public class MemberForm {
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```

**회원 컨트롤러에서 회원을 실제 등록하는 기능**

```java
@PostMapping(value = "/members/new")
public String create(MemberForm form) {

    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/";
}
```

## 회원 웹 기능 - 조회

**회원 컨트롤러에서 조회 기능**

```java
@GetMapping(value = "/members")
public String list(Model model) {
	List<Member> members = memberService.findMembers();
	model.addAttribute("members", members);
	return "members/memberList";
}
```

**회원 리스트 HTML**

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="container">
    <div>
        <table>
            <thead>
            <tr>
                <th>#</th>
                <th>이름</th>
            </tr>
            </thead>
            <tbody>
                <tr th:each="member : ${members}">
                <td th:text="${member.id}"></td>
                <td th:text="${member.name}"></td>
            </tr>
            </tbody>
        </table>
    </div>
</div> <!-- /container -->
</body>
</html>
```

### 완성 예제

![회원 웹 기능 - 조회 01](https://user-images.githubusercontent.com/52024566/123201134-0e77c500-d4ed-11eb-838a-7e7d64020788.png)
![회원 웹 기능 - 조회 02](https://user-images.githubusercontent.com/52024566/123201137-0f105b80-d4ed-11eb-8e06-2daf97eb4f02.png)