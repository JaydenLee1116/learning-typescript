# 5장. 함수

## 선택적 매개변수

선택적 객체 타입 속성과 유사하게 타입 애너테이션의 : 앞에 ?를 추가해 매개변수가 선택적임을 나타낼 수 있다.

선택적 매개변수는 반드시 마지막에 전달해줘야한다.

선택적 매개변수에는 기본적으로 undefined가 할당되기 때문에, 함수 내부에 `| undefined` 유니언 타입이 추가된다.

```tsx
const add = (first: number, second?: number) => {
  return first + (second ? second : 0);
}

---

// 아래의 코드와 위의 코드는 다르다.
// 아래 코드는 2번째 인자를 반드시 전달해줘야한다.
const add = (first: number, second: number | undefined) => {
  return first + (second ? second : 0);
}
```

## 나머지 매개변수

```tsx
function test(singer: string, ...songs: string[]) {}
```

## 명시적 반환 타입

변수와 마찬가지로 타입 애너테이션을 사용해 함수의 반환 타입을 명시적으로 선언하지 않는 것이 좋다.

매개변수 목록이 끝나는 ) 다음 혹은 함수 선언의 경우 { 앞에 배치한다.

## 그외 반환 타입

### void

함수가 어떤 값도 반환하지 않는 경우, void를 통해 반환 타입을 확인할 수 있다.

함수 타입을 선언할 때, void를 사용하면 함수에서 반환되는 모든 값이 무시된다.

JS에서의 함수는 실제값이 반환되지 않은 경우, 기본으로 모두 `return undefined`가 되지만 void는 이 `return undefined`와는 다르다. void는 반환 타입이 무시된다는 것을 의미한다.

void를 반환하도록 선언된 타입 위치에 전달된 함수가 반환된 모든 값을 무시하도록 설정할 때 유용

예시)

```tsx
const records: string[] = [];
function test(records: string[]) {
	records.forEach((record) => records.push(record));
}

// 실제 push는 배열에 값을 넣기도 하지만, 동시에 push한 값을 반환한다. 이 때, forEach는 콜백으로 받는 함수의 반환값
// void 처리되어 있기 때문에 위와 같이 쓸 수 있는 것이다.
```

### never

어떤 함수는 값을 반환하지 않고 반환할 생각도 없다. never 반환 함수는 일부러 항상 오류를 발생시키거나 무한 루프를 실행하는 함수이다. 명시적으로 `: never` 를 함으로써 해당 함수를 호출한 후 모든 코드가 실행되지 않음을 나타낸다.

```tsx
function fail(message: string): never {
	throw new Error(`에러 발생: ${message}`);
}
```

!. void는 `아무것도 반환하지 않는 함수`를 위한 것, never는 `절대 반환하지 않는 함수`를 위한 것

## 함수 오버로드

구현 시그니처와 그 함수의 본문 앞에 서로 다른 버전의 함수 이름, 매개변수, 반환 타입을 여러 번 선언

함수 오버로드는 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단이다. 즉, 가능하면 쓰지 않게 단순하고 직관적인 함수를 구현하는 게 더 좋다.

```tsx
function createDate(timestamp: number): Date;
function createDate(month: number, day: number, year: number): Date;
function createDate(month: number, day?: number, year?: number) {
	return day === undefined || year === undefined ? new Date(monthOrTimestamp) : new Date(year, monthOrTimestamp, day);
}
```

# 6장. 배열

## 배열 타입

```tsx
let basicNumbers: number[] = [1, 2, 3];
let standardNumbers: Array<number> = [1, 2, 3]; // Array class + number generic
```

## 주의사항: 불안정한 멤버

TS의 타입 시스템은 기술적으로 다소 불안정하다. 때로는 값 타입에 대한 타입 시스템의 이해가 올바르지 않을 수 있다.

아래의 코드는 실행하면 당연히 에러가 발생하지만, 이전에 타입 시스템에서 에러를 발생시키지 않는다.

```tsx
function printElementsLength(elements: string[]) {
	console.log(elements[1116].length);
}

printElementsLength(["I'm", "Jayden"]);
```

- TS의 배열 조회를 더 제한하고 타입을 안전하게 만드는 `noUncheckedIndexedAccess` 플래그가 있지만 너무 엄격해서 잘 사용하지 않는다.

## 튜플

튜플: 고정된 크기의 배열

```tsx
let nameAndAge: [string, number];
nameAndAge = ['jayden', 123];
```

TS에서 튜플 타입은 가변적인 배열보다 더 구체적으로 처리된다. 가변 길이의 배열 타입은 튜플 타입에 할당할 수 없다.

```tsx
const arr = ['jayden', 1116];
const tup: [string, number] = arr; // error / [string, number] !== (string | number)[]
```

## 튜플 추론

좀 더 구체적인 튜플 타입임을 2가지 방법으로 나타낼 수 있다.

- 명시적 튜플 타입
- const assertion

### const assertion

매번 명시적으로 튜플 타입을 써주는 건 번거로운 일이므로 `as const`를 통해 좀더 편하게 읽기 전용 튜플을 지시할 수 있다.

```tsx
const readonlyTuple = ['jayden', 1116] as const;
// readonlyTuple은 readonly인 tuple로 지정된다.
```