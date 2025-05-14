20221623 최서임
<br>
#  0. E-R Diagram & Description

![](../../Z.%20Docs/img/DBMS%20ER%20diagram%20(UML%20notation)%201.png)
<br>
해당 ERD는 편의점 체인을 나타내고 있다. 각각의 **편의점(Store)** 은 가맹/직영의 형태로 **공급업체(Vendor)** 에게서 **물품(Product)** 을 받아 **고객(Customer)** 에게 판매한다. 이때 고객 리스트는 로열티에 가입한 고객만 다룬다.
각각의 편의점은 **판매 거래(Sales Transaction)** 를 생성한다. 판매 거래는 영수증의 형태로 취급되며, 판매된 물품은 별도의 **판매 물품(Transaction Item)** 리스트를 두어 관리한다.
공급 업체에게서 물품을 받는 기준은 각각의 편의점 내의 **재고(Inventory)** 로 판단한다. 편의점은 일정 이하로 물품이 떨어지면 공급 업체에게 해당 물품의 주문을 넣는다.
<br>

# 1. Entity and Relationship Justification

## 1.1 Entity Justification

| Entity             | Justification                                                                                               |
| ------------------ | ----------------------------------------------------------------------------------------------------------- |
| `Store`            | 편의점 지점 정보와 운영 형태(가맹/직영)를 구분할 필요가 있어 Entity로 설정                                                              |
| `Product`          | UPC(`product_id`) 기준으로 관리되며, Product를 나타내는 속성이 명확히 존재하므로 Entity로 설정                                         |
| `Vendor`           | 공급업체 관리가 필요하며, 공급처마다 여러 물품을 공급할 수 있기에 별도의 Entity로 관리                                                        |
| `Inventory`        | 편의점과 물품 간 재고 수량 및 재주문 조건을 관리해야 하므로 중간 Entity로 설계<br>Weak Entity로, `store_Inventory` Relationship에 의해서 결정된다. |
| `Customer`         | 로열티 회원 관리 및 정보 수집을 위해 별도의 Entity로 설정                                                                        |
| `SalesTransaction` | 실제 거래 기록을 구분하여 관리하기 위해 Entity로 설정                                                                           |
| `TransactionItem`  | 판매 거래와 M:N의 관계이며, 수량 속성이 있기 때문에 별도로 Eneity로 분리하여 관리<br>Weak Entity로, `purchased` Relationship에 의해서 결정된다.    |
- Weak Entity는 Lucid Chart로 표현 불가능하였으므로 표에 별도로 설명하였다.
<br>

## 1.2 Relationship Justification

| Relationship      | Justification                |
| ----------------- | ---------------------------- |
| `sells`           | 매장은 물품을 판매한다.                |
| `orders`          | 물품은 공급업체로부터 주문하여 받는다.        |
| `store_inventory` | 매장은 물품에 대한 재고 정보를 가진다.       |
| `sells to`        | 매장은 고객을 상대로 물품을 판매한다.        |
| `generates`       | 매장은 물품을 판매함에 따라 판매 거래를 생성한다. |
| `purchased`       | 판매 거래 정보에는 판매된 물품들이 포함된다.    |
<br>

# 2. Mapping Cardinality Explanation

| Relationship                       | Cardinality | Explanation                   |
| ---------------------------------- | ----------- | ----------------------------- |
| Store – Product                    | 1:N         | 하나의 매장이 여러 물품 판매 가능           |
| Store – Inventory                  | 1:N         | 하나의 매장이 여러 물품의 재고를 가짐         |
| Store – SalesTransaction           | 1:N         | 한 매장에서 여러 거래 발생 가능            |
| SalesTransaction – TransactionItem | 1:N         | 하나의 거래가 여러 물품 포함              |
| Product – Vendor                   | M:N         | 하나의 제품이 여러 업체에서 공급 가능, 반대도 가능 |
| Store – Customer                   | M:N         | 고객은 여러 점포 이용 가능, 반대도 가능       |
<br>

# 3. Query Support Description

1. **Product Availability:** 
```
"Which stores currently carry a certain product (by UPC, name, or brand), and how much inventory do they have?"
```
- `Inventory`에서 `store_id`와 `pdoruct_id`를 통해 매장별 보유 상품과 수량을 확인 가능하다. 
- `Product`에는 제품의 name, brand 등의 식별 속성이 포함되어 있기에 원하는 제품을 다양한 조건으로 검색할 수 있다.
- 이를 통해 특정 제품이 어느 매장에서 얼마나 보유 중인지 조회할 수 있다.
<br>
2. **Top-Selling Items:**
```
"Which products have the highest sales volume in each store over the past month?"
```
- `SalesTransaction`은 거래 시점 `date_time`과 매장 `store_id` 정보를 기록하고 있으며,
- `TransectionItem`은 `product_id`별로 구매된 수량 `quantity`를 보유하고 있다.
- 따라서 특정 기간에 발생한 거래 중 각 제품의 판매 수량을 집계하고 이를 매장 별로 그룹화 하여 상위 판매 물품을 도출할 수 있다.
<br>
3. **Store Performance:**
```
 "Which store has generated the highest overall revenue this quarter?"
```
- 매장의 매출은 `SalesTransactions`의 `total_payment`를 통해 파악할 수 있으며, 각 거래는 특정 매장 `store_id`와 연결되어 있다.
- 따라서 `date_time`을 기준으로 `total_payment`를 집계화하면 판매량에 따른 매장 별 실적을 분석할 수 있다.
<br>
4. **Vendor Statistics:** 
```
"Which vendor supplies the most products across the chain, and how many total units have been sold?"
```
- 각 제품의 판매 수량은 `Transaction Item`의 `quentity`를 통해 집계할 수 있으며,
- `Vendor` 별로 자신이 공급한 제품의 총 판매 수량을 누적하면 가장 활발히 공급한 매장을 추려낼 수 있다.
- 이에 대해서 `offers`를 위한 별도의 Table을 생성하는 것이 필요해보인다.
<br>
5. **Inventory Reorder Alerts:**
```
"Which products in each store are below the reorder threshold and need restocking?"
```
- `Inventory`에서 각 매장에서 특정 제품에 대한 재고를 `quantity`를 통해 확인할 수 있고, `reorder_threshold`도 함께 보유하고 있다.
- 이를 통해 조건문 `quentity < reorder_threshold` 을 이용하여 재고가 부족한 항목을 파악할 수 있다.
<br>
6. **Customer Purchase Patterns:**
```
"List the top 3 items that loyalty program customers typically purchase with coffee."
```
- `Customer`는 `loyalty_id`를 통해 `SalesTransactions`과 연결되고, 각각의 거래에는 `TransactionItem`이 포함된다.
- 이를 통해 물품 이름이 `coffee`인 `TransactionItem`을 걸러내고 같은 거래에서 함께 포함된 다른 제품들의 빈도를 분석하면 자주 구매되는 상품을 확인할 수 있다.
<br>
7. **Franchise vs. Corporate Comparison:**
```
"Among franchise-owned stores, which one offers the widest variety of products, and how does that compare to corporate-owned stores?"
```
- `ownership_type`을 통해 가맹점과 직영점을 구분하고, 제품 종류는 `Invectory`를 통해 `product_id`를 set화 하여 수를 세어 확인할 수 있다.
- 이를 기반으로 가맹점 내 가장 다양한 제품을 판매하는 매장을 찾고, 이를 직영점과 비교할 수 있다.