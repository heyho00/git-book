# 관리자 기능 개발

## 기능

- 로그인
- 로그아웃
- 회원 관리
  - 회원 목록
- 카테고리 관리
  - 카테고리 목록
  - 카테고리 등록
  - 카테고리 수정
- 주문 관리
  - 주문 목록
  - 주문 상세
  - 주문 상태 변경
- 상품 관리
  - 상품 목록
  - 상품 상세
  - 상품 등록
  - 상품 수정

## 화면

1. 홈 - `/`
2. 로그인 - `/login`
3. 회원 목록 - `/users`
4. 카테고리 목록 - `/categories`
5. 카테고리 등록 - `/categories/new`
6. 카테고리 수정 - `/categories/{id}/edit`
7. 상품 목록 - `/products`
8. 상품 상세 - `/products/{id}`
9. 상품 등록 - `/products/new`
10. 상품 수정 - `/products/{id}/edit`
11. 주문 목록 - `/orders`
12. 주문 상세 - `/orders/{id}`
13. 주문 상태 변경 - `/orders/{id}/edit`
    - 주문 상태 변경을 넘어서, 배송지 수정이나 송장 번호 입력 등을 해볼 수 있다.
    - 제품화하려면 해볼 수 있는 게 아니라, **해야 한다**.

기존 작업한 코드를 최대한 재활용 해 단순 CRUD 구성할 예정.

추가한 라이브러리

- SWR

- React Hook Form

<br>

## SWR

client 단에서는 상태 관리를 아주 적극적으로 했다면,

admin에선 캐시에 집중하고 백엔드와의 동기화에 집중하는 흐름이다.

## React Hook Form

단순한 CRUD 애플리케이션을 구축한다면 Uncontrolled Component를 사용하거나, 그에 준하는 편의성을 제공하는 도구를 활용할 수 있다.

`Uncontrolled 방식`에 집중해서 활용하면 리렌더링이 줄어 커다란 폼의 성능 문제에서 유리하다.

[Uncontrolled Component](./UncontrolledComponent.md)
