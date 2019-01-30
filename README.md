# Design patterns

## Design pattern คืออะไร ?
**Design patterns** เป็นแนวคิดในการแก้ปัญหาที่เราเจอบ่อยๆในการออกแบบซอฟต์แวร์ ซึ่งถ้าเรามี `ปัญหา` แล้วปัญหานั้นมีลักษณะตรงกับ `pattern` ไหนก็ตาม เราก็จะสามารถนำแนวคิดของ pattern นั้นๆไปแก้ปัญหาของเราได้เลย  

**Pattern** แต่ละตัวจะเป็นแค่ `แนวคิดในการแก้ไขปัญหา` เท่านั้น ซึ่งมันไม่ได้บอกชัดเจนว่าเราต้องมีทำอะไรบ้างเพื่อจะแก้ปัญหาที่เจอ ดังนั้นวิธีการแก้ปัญหาที่เจอจะขึ้นกับการตัดสินใจของ developer เอง

## กลุ่มของ patterns ต่างๆ
Pattern ทั้งหมดถูกแบ่งออกเป็น 3 กลุ่ม ตามวัตถุประสงค์ในการแก้ไขปัญหาของมันดังนี้

* **Creational patterns** วัตถุประสงค์ของกลุ่มนี้คือ สร้าง object โดยที่ทำให้โปรแกรมของเรามีความยืดหยุ่นมากขึ้น และ โค้ดสามารถนำกลับมาใช้ใหม่ได้  ซึ่งภายในกลุ่มนี้มี patterns อยู่คือ
  * [Factory method pattern](FactoryMethod.md)
  * [Abstract factory pattern](AbstractFactory.md)
  * [Builder pattern](Builder.md)
  * [Singleton pattern](Singleton.md)
  * [Prototype pattern](Prototype.md)

* **Structural patterns**  
วัตถุประสงค์ของกลุ่มนี้คือ จัดการโครงสร้างของโปรแกรม เช่น class และ object แต่ละตัวจะประกอบกันยังไง โดยที่โปรแกรมจะต้องคงความยืดหยุ่นได้ และ ยังมีประสิทธิภาพเหมือนเดิม ซึ่งภายในกลุ่มนี้มี patterns อยู่คือ
  * [Adapter pattern](Adapter.md)
  * Bridge pattern
  * Composite pattern
  * Decorator pattern
  * [Facade pattern](Facade.md)
  * Flyweight pattern
  * Proxy pattern

* **Behavioral patterns**  
วัตถุประสงค์ของกลุ่มนี้คือ ดูแลประสิทธิภาพในการติดต่อกันของ objects ต่างๆ ซึ่งภายในกลุ่มนี้มี patterns อยู่คือ
  * Chain of responsibility pattern
  * Command pattern
  * Interpreter pattern
  * Iterator pattern
  * Mediator pattern
  * Memento pattern
  * Observer pattern
  * State pattern
  * Strategy pattern
  * Template method pattern
  * Visitor pattern

# Credit
https://refactoring.guru  
You can buy his book by click the image below.  
[![img](https://refactoring.guru/images/patterns/book/web-cover-en.png)](https://refactoring.guru/design-patterns/book#buy-now)  