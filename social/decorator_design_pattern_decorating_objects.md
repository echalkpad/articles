# Decorator Design Pattern - Decorating Objects

[Original URL](http://conceptf1.blogspot.ca/2016/01/decorator-design-pattern.html)

> You may also like to see: Design Patterns : Singleton - One & only ONE Object Decorator Pattern (Adapter Pattern) is a design Pattern that allows to dynamically add behavior to an existing...

**You may also like to see:**

- **[Design Patterns : Singleton - One & only ONE Object](http://conceptf1.blogspot.com/2015/04/singleton-design-pattern.html)**

Decorator Pattern (Adapter Pattern) is a design Pattern that allows to dynamically add behavior to an existing individual object without making any code changes to the underlying classes. It is flexible replacement to sub-classing for extending functionality to an object.

If you think inheritance is everything than decorator pattern help you to learn the power of extension at run-time instead of compile-time. Lets try to learn and implement decorator pattern on a problem.

## Decorator Pattern in Pizza Corner Problem

Pizza Corner growing restaurant chain around. Because they have grown, they're trying to update their ordering systems to match their Pizza offerings. While starting their business they created their classes like this.

| [![Pizza Corner order system classes](http://1.bp.blogspot.com/-1YTF9Px2edI/Vqz9fRKQmiI/AAAAAAAACF4/5b1BEPB3uaI/s400/Pizza.png "Pizza Corner order system classes")](http://1.bp.blogspot.com/-1YTF9Px2edI/Vqz9fRKQmiI/AAAAAAAACF4/5b1BEPB3uaI/s1600/Pizza.png)
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| Pizza Corner order system's classes design

Other than regular Pizza, you can ask for several pizza topping options like Bacon, Black olives, Chicken, Extra cheese, Mushrooms, Onions, Sausage<br>
and many more. Pizza Corner charges for each of these topping, so they want to add these into their system.

## First Implementation

First implementation of adding new abilities in system team created a child class for each pizza with each topping option. So first implementation looks like.<br>
[![](http://2.bp.blogspot.com/-lisgorCO6VM/Vq0FxQfGbwI/AAAAAAAACGI/pY1QwCq4fkY/s400/PizzaFirstimplementation.png)](http://2.bp.blogspot.com/-lisgorCO6VM/Vq0FxQfGbwI/AAAAAAAACGI/pY1QwCq4fkY/s1600/PizzaFirstimplementation.png)

If your first thought was also to use inheritance for this problem than you are incorrect. What if Pizza Corner started offering more toppings or added new pizza type you have to add all combination of classes in system which will become hard to manage.

### Alternate Solution

Developer team realize that this subclassed solution is not going to work and its actually a bad design so they started redesigning the system. Now they created instance variables for each topping option in the base class. So each child class can set options which it required.

[![Pizza Corner System with Instance variable](http://1.bp.blogspot.com/-ttKLn_ng4D8/Vq0OmGukCdI/AAAAAAAACGk/bd7F5sKuL5w/s400/PizzaInstaceVariable.png "Pizza Corner System with Instance variable")](http://1.bp.blogspot.com/-ttKLn_ng4D8/Vq0OmGukCdI/AAAAAAAACGk/bd7F5sKuL5w/s1600/PizzaInstaceVariable.png)

This solution reduced the size of classes but there is another problem if you have noticed now all the classes has all the topping options so Vegie can also have chicken topping which should not be allowed. Additionally this design also violate the basic design principle of **"Classes should be open**<br>
**for extension, but closed for modification."** As whenever a new topping option will be introduced we need to modify our base class which is not correct.

#### Decorator Pattern

As we have seen different approaches for our problem at Pizza Corner which have not work out very well. Lets implement Decorator Pattern in this problem.

For decorator pattern we take our pizza type object and than decorate it with different toppings. Lets say we got an order for BBQChicken with Onions, ExtraCheese and Mushrooms.

1. First take a BBQChicken object
2. Decorate it with Onions
3. Decorate it with ExtraCheese
4. Decorate it with Mushrooms
5. Call the cost() method and delegation will add cost of all topping and pizza

| [![Pizza Corner System - Decorator Pattern](http://1.bp.blogspot.com/-4uuqZA0DzEc/Vq0RnTkTgzI/AAAAAAAACGw/FWVOnUjxA3E/s400/PizzaDecorator.png "Pizza Corner System - Decorator Pattern")](http://1.bp.blogspot.com/-4uuqZA0DzEc/Vq0RnTkTgzI/AAAAAAAACGw/FWVOnUjxA3E/s1600/PizzaDecorator.png)
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| Decorator Pattern - Decorating Objects

Here are some key points for decorator pattern implementations:

- Decorators object should have the same base type as the objects they are decorating.
- We can use more than one decorators to wrap an object.
- Given that the decorator has the same supertype as the object it decorates, we can pass around a decorated object in place of the original (wrapped) object.
- The decorator object apply its own behavior either after or/and before delegating to the object it decorates.
- We can decorate objects dynamically at runtime with as many decorator as we required.

#### Code Implementation

Here is a diagram which shows the design of decorator pattern. We have to implement this design for our system. I am going to implement it in C# language.

| [![Decorator Pattern](http://4.bp.blogspot.com/-9dsSN0H7Vlw/Vq0UXQxuQ_I/AAAAAAAACG8/vY-HGW10OAQ/s400/DecoratorPattern.png "Decorator Pattern")](http://4.bp.blogspot.com/-9dsSN0H7Vlw/Vq0UXQxuQ_I/AAAAAAAACG8/vY-HGW10OAQ/s1600/DecoratorPattern.png)
| -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| Decorator Pattern - Image from Head First Design Pattern

Our base class of Pizza which will be inherited by all pizza types and toppings.<br>
public abstract class Pizza { String description = "Unknown Pizza"; public String getDescription() { return description; }

```
 public abstract double cost();
}
```

Here are our concrete components classes for each pizza type. Each concrete components will set is own definition for cost method and set description.<br>
public class BBQChicken : Pizza { public BBQChicken() { description = "BBQ Chicken"; } public double cost() { return 800; //rupees } }

```
public class HotChickenWings : Pizza
{
 public HotChickenWings () 
 {
 description = “Hot Chicken Wings”;
 }
 public double cost() 
 {
 return 750; //rupees
 }
}
public class Vegie : Pizza
{
 public Vegie() 
 {
 description = “Vegetable Pizza”; 
 }
 public double cost() 
 {
 return 650; //rupees
 }
}
```

Let implement topping decorator abstract class:<br>
public abstract class ToppingDecorator : Pizza { public abstract String getDescription(); }

We have implemented our base topping decorator class, lets implement decorator class.<br>
public class Onions : ToppingDecorator // ToppingDecorator inherit Pizza { Pizza pizza; public Onions(Pizza pizza) { this.pizza = pizza; } public String getDescription() { return pizza.getDescription() + ", Onions"; } public double cost() { return 120 + pizza.cost(); } } public class ExtraCheese : ToppingDecorator // ToppingDecorator inherit Pizza { Pizza pizza; public ExtraCheese(Pizza pizza) { this.pizza = pizza; } public String getDescription() { return pizza.getDescription() + ", ExtraCheese"; } public double cost() { return 160 + pizza.cost(); } } // similarly implement all toppings

### Serve Some Pizzas

As we have implemented decorator pattern for Pizza Corner lets serve some order of pizza and see how its going to work.

```
public class PizzaCorner
{
 public static void Main(String[] args)
 {

 Pizza pizza = new HotChickenWings();
 System.Console.WriteLine(pizza.getDescription() + “ Rs.” +pizza.cost());

 Pizza pizza2 = new BBQChicken();
 pizza2 = new Onions(pizza2);
 pizza2 = new ExtraCheese(pizza2);
 pizza2= new Mushrooms(pizza2);
 // cost triggering order is Mushrooms, ExtraCheese, Onions, BBQChicken
 System.Console.WriteLine(pizza2.getDescription()+ “ Rs” +pizza2.cost());
 }
}
```

#### Decorator Pattern in JavaScript

Lets implement the decorator pattern in JavaScript. Lets say a Cell Phone company implementing their system. They create Cell Phone with different features e.g, Camera, Wifi, 3G, Bluetooth etc each of these feature has some cost. They want to to implement their system in a way they don't need to change whole system when they introduce a new phone model with some feature. Here we going to use decorator pattern in which we take base Cell Phone object and decorate it with Feature decorator.<br>
//object we're going to decorate function CellPhone() { this.cost = function () { return 397; }; this.screenSize = function () { return 5; }; } /_Decorator 1_/ function Camera(cellPhone) { var price = cellPhone.cost(); cellPhone.cost = function() { return price + 45; } } /_Decorator 2_/ function Wifi(cellPhone){ //get cell phone current price var price = cellPhone.cost();

```
 //update cell phone cost() function
 //and add feature price to current price
 cellPhone.cost = function(){
 return price + 90;
 };
}

 /*Decorator 3*/
function threeG(cellPhone){
 var price = cellPhone.cost();
 cellPhone.cost = function(){
 return price + 90;
 };
}

/*Decorator 4*/
function Bluetooth(cellPhone){
 var price = cellPhone.cost();
 cellPhone.cost = function(){
 return price + 50;
 };
}
```

Company introduced a new model with Camera, Wifi and bluetooth. Lets see how to decorate the object:<br>
var newModelCellPhone = new CellPhone(); Camera(newModelCellPhone); Wifi(newModelCellPhone); Bluetooth(newModelCellPhone); console.log(newModelCellPhone.cost()); console.log(newModelCellPhone.screenSize());

### Conclusion

We have implemented Decorator Design Pattern which add behavior to an existing object without making any code changes to the underlying classes as you notice its follow the design principle of "Open for extension, close for modification" Now if Pizza Corner add any new Topping serving the need to extend the system by implementing new decorator class and no need to existing system. If you have any question or feedback please post in comments.

**You may also like to see:**
