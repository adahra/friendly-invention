# friendly-invention

In this project use **feature-first**.

When building large Flutter apps, one of the first things we should decide is how to structure our project.

This ensures that the entire team can follow a clear convention and add features in a consistent manner.

So in this article we'll explore two common approaches for structuring our project: **feature-first** and **layer-first**.

We'll learn about their tradeoffs and identify common pitfalls when trying to implement them on real-world apps. And we'll wrap up with a clear step-by-step guide for how you can structure your project, avoiding costly mistakes along the way.


## Project Structure in Relation to App Architecture
In practice, we can only choose a project structure once we have decided what app architecture to use.

This is because app architecture forces us to define **separate layers** with **clear boundaries**. And those layers will show up somewhere as folders in our project.

This architecture is made of four distinct layers, each containing the components that our app needs:
- **presentation**: widgets, states, and controllers
- **application**: services
- **domain**: models
- **data**: repositories, data sources, and DTOs (data transfer objects)

Of course, if we're building just a single-page app, we can put all files in one folder and call it a day. 😎

But as soon as we start adding more pages and have various data models to deal with, how can we organize all our files in a consistent way?

In practice, a **feature-first** or **layer-first** approach is often used.

So let's explore these two conventions in more detail and learn about their tradeoffs.


## Layer-first (features inside layers)
To keep things simple, suppose we have only two features in the app.

If we adopt the **layer-first** approach, our project structure may look like this:
- `lib`
  - `src`
    - `presentation`
      - `feature1`
      - `feature2`
    - `application`
      - `feature1`
      - `feature2`
    - `domain`
      - `feature1`
      - `feature2`
    - `data`
      - `feature1`
      - `feature2`

With this approach, we can add all the relevant Dart files inside each feature folder, ensuring that they belong to the correct layer (widgets and controllers inside *presentation*, models inside *domain*, etc.).


## Layer-first: Drawbacks
This approach is easy to use in practice, but doesn't scale very well as the app grows.

For any given feature, files that belong to different layers are far away from each other. And this makes it harder to work on individual features because we have to keep jumping to different parts of the project.

And if we decide that we want to delete a feature, it's far too easy to forget certain files, because they are all organized by layer.

For these reasons, the feature-first approach is often a better choice when building medium/large apps.


## Feature-first (layers inside features)
The feature-first approach demands that we create a new folder for every new feature that we add to our app. And inside that folder, we can add the layers themselves as sub-folders.

Using the same example as above, we would organize our project like this:
- `lib`
  - `src`
    - `features`
      - `feature1`
        - `presentation`
        - `application`
        - `domain`
        - `data`
      - `feature2`
        - `presentation`
        - `application`
        - `domain`
        - `data`

I find this approach more logical because we can easily see all the files that belong to a certain feature, grouped by layer.

In comparison to the layer-first approach, there are some advantages:

- whenever we want to add a new feature or modify   an existing one, we can focus on just one folder.
- if we want to delete a feature, there's only one folder to remove (two if we count the corresponding tests folder)
So it would appear that the *feature-first* approach wins hands down! 🙌

However, things are not so easy in the real world.


## What about shared code?
Of course, when building real apps you'll find that your code doesn't always fit neatly into specific folders as you intended.

What if two or more separate features need to share some widgets or model classes?

In these cases, it's easy to end up with folders called *shared* or *common*, or *utils*.

But how should these folders themselves be organized? And how do you prevent them from becoming a dumping ground for all sorts of files?

If your app has 20 features and has some code that needs to be shared by only two of them, should it really belong to a top-level *shared* folder?

What if it's shared among 5 features? Or 10?

In this scenario, there is no right or wrong answer, and you have to use your best judgement on a case-by-case basis.


## Feature-first is not about the UI!
When we focus on the UI, we're likely to think of a feature as a single page or screen in the app.

But when it came to putting the **presentation**, **application**, **domain**, and **data** layers inside them, I ran into trouble because some models and repositories were shared by multiple pages (such as the `product_page` and `product_list`).

As a result, I ended up creating top-level folders for **services**, **models**, and **repositories**:
- `lib`
  - `src`
    - `features`
      - `account`
      - `admin`
      - `checkout`
      - `leave_review_page`
      - `orders_list`
      - `product_page`
      - `products_list`
      - `shopping_cart`
      - `sign_in`
    - `models` <-- **should this go here?**
    - `repositories` <-- **should this go here?**
    - `services` <-- **should this go here?**

In other words, I had applied a **feature-first** approach to the `features` folder, which represented my entire presentation layer. But I cornered myself into a **layer-first** approach for the remaining layers, and this influenced my project structure in an unintended way.

## What is a "feature"?
So I took a step back and asked myself: "what is a feature"?

And I realized it's not about what the user sees, but what the user does:
- authenticate
- manage the shopping cart
- checkout
- view all past orders
- leave a review

In other words, a feature is a functional requirement that helps the user complete a given task.

And taking some hints from domain-driven design, I decided to organize the project structure around the domain layer.

Once I figured that out, everything fell into place. And I ended up with seven functional areas:
- `lib`
  - `src`
    - `features`
      - `address`
        - `application`
        - `data`
        - `domain`
        - `presentation`
      - `authentication`
        `...`
      - `cart`
        `...`
      - `checkout`
        `...`
      - `orders`
        `...`
      - `products`
        - `application`
        - `data`
        - `domain`
        - `presentation`
          - `admin`
          - `product_screen`
          - `products_list`
      - `reviews`
        `...`

Note that with this approach is still possible for code inside a given feature to depend on code from a different feature. For example:
- the product page shows a list of **reviews**
- the orders page shows some **product** information
- the checkout flow requires the user to **authenticate** first

But we end up with far fewer files that are shared across all features, and the entire structure is much more balanced.


## How to do feature-first, the right way
In summary, the **feature-first** approach lets us structure our project around the functional requirements of our app.

So here's how to use this correctly in your own apps:
- start from the **domain layer** and identify the model classes and business logic for manipulating them
- create a folder for each model (or group of models) that **belong together**
- within that folder, create the `presentation`, `application`, `domain`, `data` sub-folders as needed
- inside each sub-folder, add all the files you need

For reference, here's how my final project structure ended up:
- `lib`
  - `src`
    - `common_widgets`
    - `constants`
    - `exceptions`
    - `features`
      - `address`
      - `authentication`
      - `cart`
      - `checkout`
      - `orders`
      - `products`
      - `reviews`
    - `localization`
    - `routing`
    - `utils`

Without even looking inside folders such as `common_widgets`, `constants`, `exceptions`, `localization`, `routing`, and `utils`, we can guess that they all contain code that is **truly shared** across features, or needs to be **centralized** for a good reason (such as localization and routing).

And these folders all contain relatively little code.