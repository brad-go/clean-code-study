# 10장 클래스

## 목차

1. [클래스란?](#클래스란)
2. [클래스를 사용하라](#클래스를-사용하라)
3. [클래스는 작아야 한다!](#클래스는-작아야-한다)
4. [높은 응집도와 낮은 결합도](#높은-응집도와-낮은-결합도)
5. [클래스는 변경하기 쉬워야 한다](#클래스는-변경하기-쉬워야-한다)
6. [상속보다 조합을 사용하라](#상속보다는-조합을-사용하라)
7. [메서드 체이닝을 사용하라](#메서드-체이닝을-사용하라)

## 클래스란?

클래스는 **객체를 생성하기 위한 일종의 틀(템플릿)** 로써, 데이터(변수)와 이를 조작하는 코드(메서드)를 하나로 추상화한다. 클래스를 객체로 생성하는 과정을 인스턴스화라고 하며, 생성된 객체를 인스턴스라고 한다.

### 캡슐화

**비슷한, 관련된 역할을 하는 속성과 메서드들을 하나의 틀(클래스 등) 안에 담는 것**을 의미한다. 캡슐화를 하는 이유는 **정보 은닉** 때문이다. 정보 은닉이란, 객체에 대한 구체적인 정보를 노출시키지 않도록 하기 위한 기법이다. 캡슐화를 통해 잘못 사용될 수 있는 객체의 특정 부분을 사용자가 사용할 수 없게 막을 수 있다.

캡슐화를 통해 변수나 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만, 반드시 숨겨야 한다는 법칙도 없다. 테스트를 위해 `private method`나 변수를 `protected`로 공개해야 하는 경우 등이 있다. 이런 경우 공개하지 않을 방법을 충분히 생각한 이후에, 캡슐화를 풀어준다. 기억하자. **캡슐화를 풀어주는 결정은 언제나 최후의 수단**이다.

### 클래스 체계

클래스를 정의하는 표준 자바 관례에 의하면, 클래스 안에 변수나 상수, 함수 선언은 다음과 같은 순서로 정의한다. 즉, **추상화 단계가 순차적으로 내려간다**는 것이다.

```tsx
class ClassSystem {
  // 정적 공개 상수
  static readonly constant = "정적 공개 상수";

  // 정적 비공개 상수
  private static readonly privateConstant = "정적 비공개 상수";

  // 정적 비공개 변수
  private static privateVariable = "정적 비공개 변수";

  // 비공개 인스턴스 변수
  private variable = "비공개 인스턴스 변수";

  // 공개 함수
  publicFunction(): string {
    return "공개 함수";
  }

  // 비공개 함수 - 자신을 호출하는 공개 함수 직후에 정의
  private privateFunction(): string {
    return "비공개 함수";
  }
}
```

### static(스태틱)이란?

여기서 **static 멤버가 일반 인스턴스 멤버와 무슨 차이를 건데?** 라는 의문을 가질 수 있다. 우선 static 멤버(정적 멤버)는 static 변수와 static 메서드들을 의미한다. 이 정적 멤버는 인스턴스에 소속된 멤버가 아니라 클래스 자체에 고정된 멤버다.

**Static 영역에 할당된 메모리는 모든 객체가 공유하여 하나의 멤버를 어디서든지 참조할 수 있다**. 클래스의 인스턴스 없이 클래스 자체에서 호출이 가능하며, 클래스가 인스턴스화 되면 호출할 수 없다.

**정적 메서드**는 개체를 만들거나 복제하는 함수와 같은 **유틸리티 함수**에 사용되며, **정적 변수**는 **캐시, 고정 구성 또는 인스턴스 간에 복제할 필요가 없는 기타 데이터**에 유용하다.

- **장점**: 인스턴스를 생성할 때마다 힙 메모리에 올라가는 것이 아닌 고정 메모리이므로 효율적이다.
- **단점**: 무분별한 사용은 메모리 누수의 원인이 된다.

[⬆ Back to top](#목차)

## 클래스를 사용하라

코드의 표현력과 그 코드로 이루어진 함수에 아무리 신경쓸지라도 좀 더 높은 차원의 단계까지 신경쓰지 않으면 깨끗한 코드를 얻기 어렵다. **상속이 필요하거나 더 크고 복잡한 개체가 필요하다면** 클래스를 사용하자. 하지만 그 전까지는 항상 작은 함수의 사용해야 한다는 것을 잊지말자!

### Bad:

```tsx
const Animal = function (this: Animal, age: number) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function (this: Mammal, age: number, furColor: string) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function (
  this: Human,
  age: number,
  furColor: string,
  languageSpoken: string
) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

### Good:

```tsx
class Animal {
  constructor(private age: number) {}

  move() {
    // ...
  }
}

class Mammal extends Animal {
  constructor(age: number, private furColor: string) {
    super(age);
  }

  liveBirth() {
    // ...
  }
}

class Human extends Mammal {
  constructor(age: number, furColor: string, private languageSpoken: string) {
    super(age, furColor);
  }

  speak() {
    // ...
  }
}
```

[⬆ Back to top](#목차)

## 클래스는 작아야 한다!

클래스의 사이즈는 해당 클래스가 맡은 **책임**을 가지고 측정된다. [단일 책임의 원칙](https://www.notion.so/10-1-SOLID-26547029fd664621b0e032d3cfb39894)을 따라 클래스는 작아야 한다.

### Bad:

```tsx
class Dashboard {
  getLanguage(): string {
    /* ... */
  }
  setLanguage(language: string): void {
    /* ... */
  }
  showProgress(): void {
    /* ... */
  }
  hideProgress(): void {
    /* ... */
  }
  isDirty(): boolean {
    /* ... */
  }
  disable(): void {
    /* ... */
  }
  enable(): void {
    /* ... */
  }
  addSubscription(subscription: Subscription): void {
    /* ... */
  }
  removeSubscription(subscription: Subscription): void {
    /* ... */
  }
  addUser(user: User): void {
    /* ... */
  }
  removeUser(user: User): void {
    /* ... */
  }
  goToHomePage(): void {
    /* ... */
  }
  updateProfile(details: UserDetails): void {
    /* ... */
  }
  getVersion(): string {
    /* ... */
  }
  // ...
}
```

### Good:

```tsx
class Dashboard {
  disable(): void {
    /* ... */
  }
  enable(): void {
    /* ... */
  }
  getVersion(): string {
    /* ... */
  }
}

// 나머지 다른 메서드들은 다른 클래스로 나눠 책임을 분산한다.
```

[⬆ Back to top](#목차)

## 높은 응집도와 낮은 결합도

**응집도(Cohesion)** 는 **클래스 멤버들이 서로 관련이 있는 정도**를 정의한다. 이상적으로는 클래스 내의 모든 필드를 각 메서드에 사용해야 한다. 그러면 우리는 그 클래스가 응집도가 최대라고 말할 수 있다. 그러나 실제로는 이것이 항상 가능하지도 않고, 심지어 권장되지도 않는다.

그러나 우리는 응집도가 높을 수 있도록 코드를 작성해야 한다. **응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미**기 때문이다. 응집도가 높게 유지하기 위해:

1. **클래스는 인스턴스 변수 수가 작아야 한다.**
2. **각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다.**
3. 일반적으로 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 더 높다.
4. 모든 인스턴스 변수를 메서드마다 사용하는 클래스는 응집도가 가장 높다.

**결합도(커플링)** 는 **두 클래스가 서로 얼마나 관련이 있거나 의존적인지**를 의미한다. 클래스 중 하나의 변화가 다른 클래스에 영향을 미치지 않는 경우 두 클래스는 낮은 결합이라고 한다. 결합도가 낮을수록 검토해야 하는 코드도 적어져 코드 수정하기가 용이하다.

테스트가 가능할 정도로 시스템의 **결합도를 낮추면 유연성과 재사용성도 더욱 높아진다**. 결합도가 낮다는 소리는 각 시스템의 요소가 다른 요소로부터 그리고 **변경으로부터 잘 격리**되어 있다는 의미다. 결합을 최소로 줄이면 자연스럽게 또 다른 클래스 설계 원칙인 **[DIP(의존성 역전의 원칙)](./SOLID.md#L355)** 를 따르는 클래스가 나온다. 본질적으로 DIP는 클래스가 **상세한 구현이 아니라 추상화에 의존해야 한다**는 원칙이다.

### Bad:

각각의 `private` 변수가 각기 다른 메서들에서 사용된다. 이것은 클래스가 하나보다 더 많은 책임을 지고 있다는 명백한 증거다. 만약 사용자를 위한 거래들을 가져오기 위해 서비스를 만들어야 한다면, 인스턴스 변수 `emailSender`에도 영향을 줄 수밖에 없다.

```tsx
class UserManager {
  constructor(
    private readonly db: Database,
    private readonly emailSender: EmailSender
  ) {}

  async getUser(id: number): Promise<User> {
    return await this.db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await this.db.transactions.find({ userId });
  }

  async sendGreeting(): Promise<void> {
    await this.emailSender.send("Welcome!");
  }

  async sendNotification(text: string): Promise<void> {
    await this.emailSender.send(text);
  }

  async sendNewletter(): Promise<void> {
    // ...
  }
}
```

### Good:

```tsx
class UserService {
  constructor(private readonly db: Database) {}

  async getUser(id: number): Promise<User> {
    return await this.db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await this.db.transactions.find({ userId });
  }
}

class UserNotifier {
  constructor(private readonly emailSender: EmailSender) {}

  async sendGreeting(): Promise<void> {
    await this.emailSender.send("Welcome!");
  }

  async sendNotification(text: string): Promise<void> {
    await this.emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

이렇게 몇몇 함수가 몇몇 변수만 사용한다면 독자적인 클래스로 분리한다. 즉, **클래스가 응집력을 잃는다면 쪼개라**! 그래서 큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스 여럿으로 쪼갤 기회가 생긴다. 그러면서 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

[⬆ Back to top](#목차)

## 클래스는 변경하기 쉬워야 한다

대다수 시스템은 지속적인 변경이 가해진다. 그리고 무너가 변경할 때마다 시스템이 의도대로 동작하지 않을 위험이 따른다. 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.

**새 기능을 수정하거나 기존 기능을 변경할 때, 건드릴 코드가 최소인 시스템 구조가 바람직하다**. 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지 않는다. 즉, **[개방 폐쇄의 원칙](https://www.notion.so/10-1-SOLID-26547029fd664621b0e032d3cfb39894)** 을 지원한다.

### Bad:

새로운 SQL문을 지원하려면 반드시 Sql 클래스에 손대야 한다. 또, 기존 SQL문 하나를 수정할 때도, 반드시 Sql 클래스에 손대야 한다. 이렇듯 변경할 이유가 두가지 이므로 SRP를 위반한다.

```tsx
class Sql {
  constructor(table: string, columns: Column[]) {}
  create(): string {
    /* ... */
  }
  insert(fields: Object[]): string {
    /* ... */
  }
  selectAll(): string {
    /* ... */
  }
  findByKey(keyColumn: string, keyValue: string): string {
    /* ... */
  }
  select(column: Column, pattern: string): string;
  select(criteria: Criteria): string;
  select(column?: Column, pattern?: string, criteria?: Criteria): string {
    /* ... */
  }
  preparedInsert(): string {
    /* ... */
  }
  private columnList(columns: Column[]) {
    /* ... */
  }
  private valuesList(fields: Object[], columns: Column[]) {
    /* ... */
  }
  private selecteWithCriteria(criteria: string): string {
    /* ... */
  }
  private placeholderList(columns: Column[]): string {
    /* ... */
  }
}
```

### Good:

```tsx
abstract class Sql {
  constructor(table: string, columns: Column[]) {}
  abstract generate(): string;
}

class CreateSql extends Sql {
  constructor(table: string, columns: Column[]) {
    super(table, columns);
  }
  generate(): string {
    /* ... */
  }
}

class SelectSql extends Sql {
  constructor(table: string, columns: Column[]) {
    super(table, columns);
  }
  generate(): string {
    /* ... */
  }
}

class InsertSql extends Sql {
  constructor(table: string, columns: Column[], fields: Object[]) {
    super(table, columns);
  }
  generate(): string {
    /* ... */
  }
  private valuesList(fields: Object[], columns: Column[]): string {
    /* ... */
  }
}

class SelectWithCriteriaSql extends Sql {
  constructor(table: string, columns: Column[], criteria: Criteria) {
    super(table, columns);
  }
  generate(): string {
    /* ... */
  }
}

class FindeByKeySql extends Sql {
  constructor(
    table: string,
    columns: Column[],
    keyColumn: string,
    keyValue: string
  ) {
    super(table, columns);
  }
  generate(): string {
    /* ... */
  }
}

class PreparedInsertSql extends Sql {
  constructor(table: string, columns: Column[]) {
    super(table, columns);
  }
  generate(): string {
    /* ... */
  }
  private placehorderList(columns: Column[]): string {
    /* ... */
  }
}

class Where {
  constructor(criteria: string) {}
  generate(): string {
    /* ... */
  }
}

class ColumnList {
  constructor(columns: Column[]) {}
  generate(): string {
    /* ... */
  }
}
```

[⬆ Back to top](#목차)

## 상속보다는 조합을 사용해라

Gang of Four의 디자인 패턴에서 유명한 것처럼, 가능한 한 **상속(Inheritance)** 보다는 **조합(Composition)** 을 사용하려고 해야 한다. 상속을 사용해야 할 좋은 이유와 조합을 사용해야 할 좋은 이유가 많이 있다. 이 말의 요점은 본능적으로 상속을 추구한다면, 조합이 마주한 문제를 더 잘 모형화 할 수 있는지 생각하려고 노력하라는 것이다.

그러면 “언제 상속을 사용해야 할까?”하고 궁금해 할 수 있다. 그것은 당면한 문제에 따라 다르지만, 다음은 상속이 조합보다 더 의미 있는 경우다.

1. 상속은 ‘`has-a`’ 관계가 아닌 ‘`is-a`’ 관계를 나타낸다. (사용자 → 사용자 정보 vs 사람 → 동물)
2. 기본 클래스의 코드를 재사용할 수 있다. (인간은 모든 동물처럼 이동할 수 있다.)
3. 기본 클래스를 변경하여 파생 클래스를 전체적으로 변경하려고 한다. (모든 동물이 이동할 때 칼로리 소모량을 변경한다.)

### Bad:

`Employee`가 `tax` 데이터를 가지고 있기 때문에 좋은 코드가 아니다. `EmployeeTaxData`는 `Employee` 타입이 아니다.

```tsx
class Employee {
  constructor(private readonly name: string, private readonly email: string) {}
  // ...
}

class EmployeeTaxData extends Employee {
  constructor(
    name: string,
    email: string,
    private readonly ssn: string,
    private readonly salary: number
  ) {
    super(name, email);
  }
  // ...
}
```

### Good:

```tsx
class Employee {
  private taxData: EmployeeTaxData | null = null;

  constructor(private readonly name: string, private readonly email: string) {}

  setTaxData(ssn: string, salary: number): Employee {
    this.taxData = new EmployeeTaxData(ssn, salary);
    return this;
  }
}

class EmployeeTaxData {
  constructor(readonly ssn: string, readonly salary: number) {}
}
```

[⬆ Back to top](#목차)

## 메서드 체이닝을 사용하라

이 패턴은 매우 유용하며 많은 라이브러리에서 흔히 사용된다. 이것은 코드를 표현적이고, 덜 상세하게 만들어준다. 이러한 이유로 메서드 체이닝을 사용하고 코드가 얼마나 깨끗해지는지 살펴보자.

### Bad:

```tsx
class QueryBuilder {
  private collection: string | null = null;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): void {
    this.collection = collection;
  }

  page(number: number, itemsPerPage: number = 100): void {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
  }

  orderBy(...fields: string[]): void {
    this.orderByFields = fields;
  }

  build(): Query {
    // ...
  }
}

const queryBuilder = new QueryBuilder();
queryBuilder.from("users");
queryBuilder.page(1, 100);
queryBuilder.orderBy("firstName", "lastName");
```

### Good:

```tsx
class QueryBuilder {
  private collection: string | null = null;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): this {
    this.collection = collection;
    return this;
  }

  page(number: number, itemsPerPage: number = 100): this {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
    return this;
  }

  orderBy(...fields: string[]): this {
    this.orderByFields = fields;
    return this;
  }

  build(): Query {
    // ...
  }
}

const queryBuilder = new QueryBuilder()
  .from("users")
  .page(1, 100)
  .orderBy("firstName", "lastName")
  .build();
```

[⬆ Back to top](#목차)
