---
layout: post
title: "Architecture Components"
date: 2025-05-30 10:49
comments: true
categories: [architecture, software, programming, plantuml]
sharing: true
published: true
---

Thoughts on architecture components and software stability.  
In this article we also discuss one approach to evaluating architecture quality.

<!-- more -->

> Software Architecture is about drawing boxes and arrows.  
>                                  -- Some architect

## Architecture Quality Challenge

Evaluating software architecture is difficult. Unlike performance or test coverage, architecture lacks clear, objective metrics. Most assessments rely on expert judgment, which are often subjective and inconsistent.

This is a problem because architecture has a huge impact on maintainability, scalability, and delivery speed. To move beyond intuition, we need some indicators to measure architecture based on component relationships, dependencies, and change impact. This article explores one approach, using graph-based analysis to uncover hidden complexity and assess architectural quality.

## Architecture Component (box)

```plantuml
rectangle "component"
```

An architecture component is a unit of software with a clearly defined responsibility and interface.  
In diagrams, it is "a box" - but its physical form can vary depending on the architecture style and point of view. It might be a folder in a monorepo, a class module, a shared library, or a containerized microservice with its own database.

Regardless of form, a component has two essential traits:

 - Encapsulates functionality
 - Exposes an interface

Well-designed components also tend to be:

 - Reusable in different contexts
 - Interdependent, working with other components to fulfill business scenarios

In other words, components divide complexity and create boundaries that make the system understandable and maintainable. They are the atoms of architecture.

## Dependency (arrow)

Components rarely operate alone. They collaborate by calling each other’s interfaces, forming dependencies. In architectural diagrams, these relationships are shown as arrows: if component A uses component B, we draw an arrow from A to B.

```plantuml
rectangle "A"
rectangle "B"

A -> B
```

The **direction** of the arrow is crucial. It tells us who depends on whom. 

If we change A (e.g. adjust its logic or interface usage), only A needs to be tested. 

```plantuml
rectangle "A" #red
rectangle "B"

A -> B
```

But if we change B (e.g. modify its interface or data model), this may affect all components that depend on it, including A:

```plantuml
rectangle "A" #pink
rectangle "B" #red

A -> B
```

This makes B more critical in the architecture - it sits upstream, and changes to it have a wider impact. Components with many inbound arrows often need stronger contracts, better test coverage, and more cautious deployment strategies.

### Transitive Dependency

Dependencies can be transitive. If A depends on B, and B depends on C, then A indirectly depends on C

```plantuml
rectangle "A" #pink
rectangle "B" #pink
rectangle "C" #red

A -> B
B -> C
```

In this setup, a change in C can go through B and break A, even if A has no direct connection to C. These chains of dependencies increase the system’s fragility and make reasoning about change more complex.

### Circular Dependency

Circular dependencies are even more sneakier. If components form a loop A -> B -> C -> A - then each one indirectly depends on itself.

```plantuml
rectangle "A" #red 
rectangle "B" #red
rectangle "C" #red

A -> B
B -> C
C -> A
```

Just think how fragile it is. This breaks modularity. A change in any component affects the entire cycle. Worse, the cycle hides these effects, making them difficult to test or isolate. Circular dependencies should be avoided or broken explicitly through architectural refactoring.


## Case Study: Website **"Go Bananas"**

Let’s apply our ideas to a concrete example. Imagine a grocery delivery website called "Go Bananas". From a user’s perspective, it’s simple: they open the site, find a product, check availability, add it to the cart, place an order, pay, and wait for delivery.

![](/images/arch/gobananas/gobananas.jpg)

But architecture is about perspective. For the end user, it's just one system. For a UI/UX designer, it might break down into two broad areas: the UI and "everything else."

A software architect, however, sees a more complex structure - a system made up of distinct components, each responsible for a part of the functionality. Let's break the requirements down and map them to functional units:

- "User opens website" — There needs to be a frontend that displays product information. This is typically called the `ecommerce` or website component.
- "Find a product" — We need a list of products, so we introduce a `products` component.
- "Check availability" — Knowing how many bananas are left in stock is different from knowing what types of bananas we sell. Availability changes frequently, while product definitions do not. This leads us to split out an `assortment` component, responsible for stock levels.
- "Place an order" — Requires an `orders` component.
- "Pay for it" — This process is handled by some `payments` component.
- "Wait to be delivered" — Managed by a `delivery` component.

From a single user actions sequence, we now have a system composed of six  components:

```plantuml
rectangle "ecommerce"
rectangle "products"
rectangle "assortment"
rectangle "orders"
rectangle "payments"
rectangle "delivery"
```

## Component diagram (_boxes and arrows_)

A component diagram is a visual representation of a system’s structure. It uses the basic architectural building blocks we’ve discussed: boxes for components and arrows for dependencies. It shows how different parts of the system interact and rely on each other to fulfill business processes. Such diagram is a key tool for analyzing architecture - it reveals dependency chains, coupling, and potential hotspots that affect maintainability.

Now let’s return to our components in the "Go Bananas" system and connect them with arrows to form a component diagram.

- User -> Ecommerce  
The user "depends" on the website. If the site changes (e.g. new layout or navigation), the user experience is affected and may require adjustment or relearning.

- Ecommerce -> Products, Assortment  
To render the UI, the ecommerce component pulls product information (names, images, prices) from the products component, and stock availability from assortment.

- Ecommerce -> Orders  
Once the user builds their cart, the selected items are sent to the orders component to create a new order.

- Assortment -> Products  
Stock information in assortment must be linked to specific products. To register an item in stock, we need to know what that item is.

- Orders -> Products, Assortment  
The orders component validates the incoming cart - checking that all products exist and that enough stock is available.

- Orders -> Payments  
Once the order is created, the user is directed to the payments component to complete the transaction.

- Orders -> Delivery  
After payment and confirmation, the orders component schedules delivery via the delivery component.

Here is the complete diagram:

```plantuml
actor "user"
rectangle "ecommerce"
rectangle "products"
rectangle "assortment"
rectangle "orders"
rectangle "payments"
rectangle "delivery"

user -> ecommerce
ecommerce -down-> products
ecommerce -down-> assortment

assortment -left-> products 

ecommerce -down--> orders

orders -up-> products
orders -up-> assortment

orders -left-> payments
orders -right-> delivery
```

This diagram captures both component structure and data flow, which are key to understanding how the architecture behaves during change. At this stage, we’re not judging whether this is “good” or “bad” architecture - we're focused on mapping the relationships clearly. This sets the foundation for analyzing system maintainability.

## Maintainability Use Cases 

What happens if we change something?

We’ll walk through a few scenarios and highlight all components affected by the change, directly or transitively.

### Case A: Change in `ecommerce`

Let’s say we update the UI layout, add a filter, or redesign how items appear to the user. This affects only the ecommerce component. Since it consumes data from other components but doesn’t expose its own interface, the change is isolated.

```plantuml
actor "user"
rectangle "ecommerce" #red
rectangle "products"
rectangle "assortment"
rectangle "orders"
rectangle "payments"
rectangle "delivery"

user -> ecommerce
ecommerce -down-> products
ecommerce -down-> assortment
assortment -left-> products 
ecommerce -down--> orders
orders -up-> products
orders -up-> assortment
orders -left-> payments
orders -right-> delivery
```

Impact: Low. `ecommerce` can evolve independently as long as its usage of other components remains unchanged.

### Case B: Change in `products`

Now imagine we change the product schema - rename fields, restructure data, or split categories. This breaks consumers of the products component, namely ecommerce, orders, and assortment.

```plantuml
actor "user"
rectangle "ecommerce" #pink
rectangle "products" #red
rectangle "assortment" #pink
rectangle "orders" #pink
rectangle "payments"
rectangle "delivery"

user -> ecommerce
ecommerce -down-> products
ecommerce -down-> assortment
assortment -left-> products 
ecommerce -down--> orders
orders -up-> products
orders -up-> assortment
orders -left-> payments
orders -right-> delivery
```

Impact: High. products is upstream of multiple components. Any changes must be coordinated and tested across the system.

### Case C: Change in `orders`

Suppose we redesign the order lifecycle or adjust how order validation works. This affects only `ecommerce` (which sends orders).

```plantuml
actor "user"
rectangle "ecommerce" #pink
rectangle "products"
rectangle "assortment"
rectangle "orders" #red
rectangle "payments"
rectangle "delivery"

user -> ecommerce
ecommerce -down-> products
ecommerce -down-> assortment
assortment -left-> products 
ecommerce -down--> orders
orders -up-> products
orders -up-> assortment
orders -left-> payments
orders -right-> delivery
```

Impact: Medium. Despite `orders` is a central component with many inbound and outbound connections, the way we designed it has pretty isolated changes.

### Case D: Change in `payments`

We might update payment provider integration, add fraud detection, or restructure transaction handling. Since payments is used only by orders, the change is relatively contained.

```plantuml
actor "user"
rectangle "ecommerce" #pink
rectangle "products"
rectangle "assortment"
rectangle "orders" #pink
rectangle "payments" #red
rectangle "delivery"

user -> ecommerce
ecommerce -down-> products
ecommerce -down-> assortment
assortment -left-> products 
ecommerce -down--> orders
orders -up-> products
orders -up-> assortment
orders -left-> payments
orders -right-> delivery
```

Impact: Medium. Changes in payments may require minor adjustments in orders, but are unlikely to cascade further.

This type of impact analysis is a powerful tool for objectively evaluating maintainability. The more isolated changes can be, the more maintainable the architecture is. Components with high fan-in or fan-out are more expensive to modify and should be designed and managed with extra care.

## "Hot" Components

Now that we’ve explored how changes in one component can affect others, let’s look at the architecture through a more quantitative lens. Some components are hotspots - they are connected to many others and become bottlenecks for development and maintenance. The more central a component is in the dependency graph, the more "expensive" it is to change.

We can start measuring this by calculating three metrics for each component:

- IC (Number of Inbound Components) How many other components directly depend on this one.
- OC (Number of Outbound Components) How many other components this one depends on.

These metrics help identify overloaded components - ones with high inbound or outbound coupling - which can signal risk, fragility, or the need for architectural refactoring.

### Metrics for "Go Bananas"

Let’s apply this to our current component diagram:

```plantuml
actor "user"
rectangle "ecommerce"
rectangle "products"
rectangle "assortment"
rectangle "orders"
rectangle "payments"
rectangle "delivery"

user -> ecommerce
ecommerce -down-> products
ecommerce -down-> assortment
assortment -left-> products 
ecommerce -down--> orders
orders -up-> products
orders -up-> assortment
orders -left-> payments
orders -right-> delivery
```

```
Component     | IC (Inbound)                         | OC (Outbound)
--------------|--------------------------------------|-----------------------------------------------
ecommerce     | 1 (user)                             | 3 (products, assortment, orders)
products      | 3 (ecommerce, assortment, orders)    | 0
assortment    | 2 (ecommerce, orders)                | 1 (products)
orders        | 1 (ecommerce)                        | 4 (products, assortment, payments, delivery)
payments      | 1 (orders)                           | 0
delivery      | 1 (orders)                           | 0
```

### Observations

- `orders` is the most connected component. It has the highest outbound count (OC = 4), depending on `products`, `assortment`, `payments`, and `delivery`. It also has inbound connections from `ecommerce`. This makes orders a clear hotspot - any change to it risks impacting multiple other components.
- `products` has the highest inbound count (IC = 3), meaning it is relied on by `ecommerce`, `assortment`, and `orders`. While it doesn’t depend on anything itself, its central position makes it fragile: changing it can break multiple downstream components.
- `assortment` plays a mid role - used by `ecommerce` and `orders`, while depending on `products`. It links catalog data to inventory and order logic, making it moderately sensitive to change.
- `ecommerce`, `payments`, and `delivery` are more isolated. They either consume other services or are used in a limited way. As a result, they’re generally safer to change without wide system impact.

### Why This Matters

Overloaded components can slow down teams. A single change may require coordination with multiple services, broader test coverage, and longer release cycles. These components tend to accumulate complex logic, become harder to reason about, and resist refactoring.

By identifying such components early using simple dependency metrics like Inbound (IC) and Outbound (OC), we can:

- Split responsibilities into smaller, clearer modules
- Introduce anti-corruption layers to isolate fragile contracts
- Improve testability by decoupling logic
- Reduce unexpected ripple effects during change

In short, structural metrics turn architectural intuition into actionable insight - giving teams a way to spot problems before they become blockers.    