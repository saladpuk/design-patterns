![img](assets/command/command.png)

# Command
Encapsulate a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

## 🎯 เป้าหมายของ pattern นี้
เปลี่ยน Action ต่างๆให้กลายเป็น object ทำให้โปรแกรมจัดการ action ที่เข้ามาได้หลายรูปแบบ เช่น Undo, Redo

## ✌ หลักการแบบสั้นๆ
ตัวนี้ยาวมากรบกวนไปอ่านเองละกัน ใครสรุปสั้นๆได้บอกผมด้วย (Fork ออกไปแล้ว pull request เข้ามาก็ได้)

## 😢 ปัญหา
วันนี้นึกสนุก เลยเขียนโปรแกรม Text editor ขึ้นเล่นๆตัวนึง ซึ่งภายในโปรแกรมนี้จะมี Toolbar อยู่ด้านบน ซึ่งภายในนั้นจะมีปุ่มต่างๆให้กดมากมาย ซึ่งปุ่มทุกปุ่มเราสร้างมาจาก `Button` class ตามรูป

![img](assets/command/problem1.png)

เนื่องจากปุ่มที่อยู่บน toolbar มันมีการทำงานหลายแบบซึ่งไม่เหมือนกันเลย เราเลยต้องสร้าง subclass ของมันออกมา เพื่อให้มันทำงานในรูปแบบต่างๆกันได้ เช่น `ปุ่มตกลง`, `ปุ่มยกเลิก`, `ปุ่มบันทึก` บลาๆ ตามรูปด้านล่าง

![img](assets/command/problem2.png)

เมื่อเขียนเสร็จ เราก็พึ่งเอะใจว่า นั่นมันโค้ดบ้าอะไรกันฟระเนี่ย subclass ยั้วเยี้ยเต็มไปหมด หยั่งกะแมลงสาบ!! ยิ่งมีปุ่มบน toolbar เพิ่มขึ้นมาเท่าไหร่ subclass ก็จะยิ่งมากขึ้นเท่านั้น

และยังมีเรื่องน่าเกลียดอีกเรื่อง เมื่อเรามองปุ่มต่างๆในโปรแกรมเราแล้วจะเห็นว่า **ปุ่มบันทึก** ที่อยู่บน toolbar, **ปุ่มบันทึก** ที่อยู่บนเมนู หรือแม้กระทั่ง **ปุ่มบันทึก** ที่อยู่ในเมนูลัด ทั้งหมดจริงๆมันก็คือโค้ดตัวเดียวกันนิหว่า!! เราแค่ไปสร้าง subclass ใหม่ แล้ว copy โค้ดเดิมไปใส่ใน subclass พวกนั้นเท่านั้นเอง ไม่มีอะไรต่างกันเลยนิหว่า!!

![img](assets/command/problem3.png)

ในตอนนี้ Button class มันผิดกลับที่ตั้งใจไว้ในตอนแรก เพราะช่วงแรกๆที่ออกแบบไว้ ปุ่มบันทึกมันมีแค่ใน toolbar เท่านั้น แต่พอทำไปซักพัก มันก็เริ่มมีปุ่มบันทึกตรงนั้น ตรงนู้น ตรงนี้ เพิ่มเข้ามาเรื่อยๆ มันเลยทำให้โค้ดเรา `มีกลิ่นไม่ดี` ยิ่งขึ้นไปเรื่อยๆ

> โดเรม่อนวันนี้เสนอตอน **โค้ดมีกลิ่นไม่ดี** (เคยดมมั่นปะ?)  
เรื่องของการที่โค้ดมีกลิ่นไม่ดีนั้น เป็นคำที่เอาไว้พูดถึงโค้ดที่มีแนวโน้มว่าจะมีปัญหาในอนาคตสูง เช่น โค้ดเดียวกันถูกเขียนซ้ำๆหลายๆที่, ออกแบบแล้วคลาสใหญ่ม๊วกกกก บลาๆ ปัญหาพวกนี้เรามีเทคนิกที่ช่วยตรวจในเบื้องต้นที่เรียกกันว่า `Code smell` นั่นเอง (ไปหาอ่านต่อกันเองเด้อถ้าว่างอาจจะเอามาแนะนำ)

แล้วเราจะแก้ปัญหากลิ่นคาวปลานี้ยังไงดีละ? (แหวะ)

## 😄 วิธีแก้ไข
ก่อนที่จะไปต่อขออธิบายหลักปฎิบัติที่ชื่อว่า Separation of concerns (SoC) นิ๊สนุง

> **Separation of concerns (SoC)**  
คือหนึ่งใน design principle ซึ่งหลักการมีอยู่ว่า เราควรจะแยกงานออกเป็นส่วนๆ ตามหน้าที่การรับผิดชอบของมัน (จบเท่านี้ละกันเดี๋ยวจะยาว ไปหาอ่านต่อเองนะจุ๊)

ดังนั้น ถ้าเรามองกลับไปว่ามันเกิดอะไรขึ้นกับโค้ดเรา เราก็จะพบว่า `เมื่อผู้ใช้กดปุ่มเซฟที่หน้า UI สิ่งที่มันเกิดขึ้นคือ มันจะไปเรียกใช้ method บันทึกโดยตรงได้เลย` นั่นหมายความว่ามันขัดกับหลักการของ Separation of concerns เลยนิน่า (ยังไงหว่า?)

งั้นอธิบายต่ออีกนิสกะด๊าย

> **ตัวอย่างการผิดหลัก Separation of concerns (SoC)**  
โดยปรกติการแสดงผลต่างๆของโปรแกรม เช่นหน้าตาความสวยงาม เราจะแยกมันออกเป็น concern ในด้าน UI layer (เพราะงานมันคือต้องดูแลความสวยงามเพียงอย่างเดียวเท่านั้น) แต่การที่เมื่อปุ่มถูกกด มันกลับไปเรียกใช้การบันทึกได้ตรงๆเลย ซึ่งเรื่องการบันทึกเป็นงานฝั่ง Business logic layer ต่างหาก ดังนั้นการที่ UI ไปบันทึกข้อมูลได้ มันเลยเป็นการเอางานของ Business logic มาไว้ใน UI มันเลยผิดหลักการเพราะไม่ทำการแยก concern ออกจากกัน

จากที่ UI layer ไปเรียก Business logic layer ตรงๆ พอผู้ใช้กดปุ่มบันทึกมันเลยทำให้โค้ดเรามันออกมาเป็นภาพนี้

![img](assets/command/solution1-en.png)

ซึ่งแนวคิดของ `Command Pattern` เสนอว่า ให้เปลี่ยนของที่ผู้ใช้กระทำ `(Action)` มาเป็น object ซะ และถ้ามันต้องมีข้อมูลอะไรที่เกี่ยวข้อง ก็ให้ยัดเข้ามาใน object นั้นด้วยเลย ซึ่ง object พวกนั้นเราเรียกมันว่า `Command` นั่นเอง

เมื่อปุ่มถูกกดบันทึก มันจะไม่บันทึกข้อมูลแต่มันจะไปสร้าง command object ขึ้นมา ซึ่งเจ้าตัว command object นี้แหละจะเป็นเสมือนตัวเชื่อมระหว่าง UI layer กับ Business logic layer

เมื่อเป็นแบบนี้จะทำให้ UI layer แยกจาก Business logic layer แล้ว ตามภาพเลย

![img](assets/command/solution2-en.png)

ตัว Command ทุกตัวจะ implement `Interface` เดียวกัน ซึ่งเจ้า interface นั้นจะมีแค่ `Execute()` method เท่านั้น ทำให้คนที่ส่ง/รับ command ไม่ต้องผูกติด (coupling) กับ concrete command ใดๆ

แล้วข้อมูลต่างๆที่จะส่งไปกับ command ละ? เช่นกดเปลี่ยนเป็นสีเหลือง หรือเปลี่ยนเป็นสีแดง มันจะต้องส่ง `สี` เป็น parameter ไปด้วยนิน่า

คำตอบคือ เจ้าตัว command เองนั่นแหละที่มีข้อมูลเหล่านั้นอยู่ด้วย ตามภาพเลย

![img](assets/command/solution3.png)

ในตอนนี้เวลาที่เราจะสั่งให้มันทำอะไร เราก็จะเปลี่ยนคำสั่งให้อยู่ในรูปของ `Command object`

ส่วนคนที่สร้าง command object จะส่ง object นี้ไปให้กับสิ่งที่เรียกว่า `Invoker` ซึ่งเจ้า invoker นี้จะสั่งให้ command object ทำงานต่ออีกทีหนึ่ง

และสุดท้าย command object ก็จะส่งงานต่อไปให้กับตัวที่ทำงานที่แท้จริง นั่นคือตัวที่ชื่อว่า `Receiver` นั่นเอง

สรุป แทนที่เราจะให้ UI ทำงานตรงๆเลย เราจะมีกลุ่มของ command หลายๆตัว ซึ่ง command แต่ละตัวจะรู้ว่า เมื่อจะต้องทำงาน มันจะต้องส่งงานต่อให้กับ receiver ตัวไหนไปทำงานต่อ และเมื่อ UI จะสั่งให้ทำงาน มันจะสั่งงานผ่าน invoker นั่นเอง

## 📌 โครงสร้างของ pattern นี้
![img](assets/command/structure-2x.png)

> **อธิบาย**  
**1.Invoker** - เก็นคนที่เก็บ command ต่างๆที่มี ไว้ให้ client เรียกใช้เมื่อต้องการ โดยมันจะสั่งให้ command object ไปทำงานต่อ  
**2.Command** - เป็น interface มาตรฐานให้กับ concreate command ปรกติจะมีแค่ Execute() method เดียว  
**3.Concrete Commands** - เป็นตัวเก็บข้อมูลของงานที่จะทำ และรู้ว่า receiver ตัวไหนจะมารับงานไปทำต่อ  
**4.Receiver** - คือคนที่เอางานไปทำจริงๆ (Business logic จะอยู่ที่นี่)  
**5.Client** - เป็นคนสร้าง concrete command object เพื่อส่งต่อไปให้กับ invoker ทำงาน

## 🛠 ตัวอย่างการนำไปใช้งาน
สมมุติว่าผมต้องการเขียนโปรแกรม รีโมตทีวี โดยมันจะต้อง เปลี่ยนช่องทีวี, เปิดทีวี, ปิดทีวี ได้ และยังรองรับคำสั่ง Undo และ Redo ด้วย (เจ๋งปะละ?)

ปะไปดูโค้ดตัวอย่างกัน

## 👍 ข้อดี
* ทำให้โปรแกรมรองรับ Undo/Redo (CTRL+Z กับ CTRL+Y)
* สามารถสั่งงาน command เมื่อไหร่ก็ได้ ไม่จำเป็นต้องให้มันทำทันที

## 👎 ข้อเสีย
* เพิ่มความซับซ้อนให้กับโค้ด เพราะต้องไปสร้าง class มากมาย

## ‍‍📝 Code ตัวอย่าง
```
using System;
using System.Linq;
using System.Collections.Generic;

// Command Interface
interface ICommand
{
    void Execute();
    void Undo();
    void Redo();
}

// Concrete Commands
class TurnOnTelevisionCommand : ICommand
{
    private Television tv;

    public TurnOnTelevisionCommand(Television tv)
        => this.tv = tv;

    public void Execute()
        => tv.TurnOn();

    public void Undo()
        => tv.TurnOff();

    public void Redo()
        => tv.TurnOn();
}
class TurnOffTelevisionCommand : ICommand
{
    private Television tv;

    public TurnOffTelevisionCommand(Television tv)
        => this.tv = tv;

    public void Execute()
        => tv.TurnOff();

    public void Undo()
        => tv.TurnOn();

    public void Redo()
        => tv.TurnOff();
}
class ChangeChannelCommand : ICommand
{
    private Television tv;
    private int channel;
    private int previousChannel;

    public ChangeChannelCommand(Television tv, int channel)
    { 
        this.tv = tv;
        this.channel = channel;
    }

    public void Execute()
    {
        previousChannel = tv.CurrentChannel;
        tv.ChangeChannel(channel);
    }

    public void Undo()
        => tv.ChangeChannel(previousChannel);

    public void Redo()
        => tv.ChangeChannel(channel);
}

// Receiver
class Television
{
    public int CurrentChannel { get; private set; }

    public void TurnOff()
        => Console.WriteLine("Tv ปิด");

    public void TurnOn()
        => Console.WriteLine("Tv เปิด");
    
    public void ChangeChannel(int channel)
    {
        CurrentChannel = channel;
        Console.WriteLine($"เปลี่ยนช่อง Tv เป็นช่อง {channel}");
    }
}

// Invoker
class TelevisionRemoteInvoker
{
    private IList<ICommand> undoStack;
    private IList<ICommand> redoStack;

    public TelevisionRemoteInvoker()
    {
        undoStack = new List<ICommand>();
        redoStack = new List<ICommand>();
    }

    public void Execute(ICommand cmd)
    {
        cmd.Execute();
        undoStack.Add(cmd);
    }

    public void Undo()
    {
        if(!undoStack.Any())
            return;
        
        var previousCmd = undoStack.Last();
        previousCmd.Undo();
        undoStack.Remove(previousCmd);
        redoStack.Add(previousCmd);
    }

    public void Redo()
    {
        if(!redoStack.Any())
            return;
        
        var previousCmd = redoStack.Last();
        previousCmd.Redo();
        redoStack.Remove(previousCmd);
        undoStack.Add(previousCmd);
    }
}

// Client
class Program
{
    static void Main(string[] args)
    {
        var tv = new Television();
        var remote = new TelevisionRemoteInvoker();
        Console.Write("สั่งเปิด, ");
        remote.Execute(new TurnOnTelevisionCommand(tv));

        Console.Write("สั่งปิด, ");
        remote.Execute(new TurnOffTelevisionCommand(tv));

        Console.Write("ยกเลิกที่สั่งไปเมื้อกี้ (CTRL+Z), ");
        remote.Undo();

        Console.Write("สั่งเปลี่ยนช่อง, ");
        remote.Execute(new ChangeChannelCommand(tv, 72));

        Console.Write("สั่งเปลี่ยนช่อง, ");
        remote.Execute(new ChangeChannelCommand(tv, 55));

        Console.Write("ยกเลิกที่สั่งไปเมื้อกี้ (CTRL+Z), ");
        remote.Undo();
        
        Console.Write("ทำซ้ำ (CTRL+Y), ");
        remote.Redo();
    }
}
```

> **Note**  
สำหรับภาษา C# นั้นทีมพัฒนาได้เตรียม interface สำหรับทำ Command ไว้ให้แล้วนะโยมนะ ซึ่ง interface ตัวนั้นชื่อว่า `ICommand` โยมไม่ต้องไปเขียนใหม่ให้เมื่อยมือหรอก [กดที่นี่เพื่ออ่านต่อ](https://docs.microsoft.com/en-us/dotnet/api/system.windows.input.icommand?view=netframework-4.7.2)

**Output**
```
สั่งเปิด, Tv เปิด
สั่งปิด, Tv ปิด
ยกเลิกที่สั่งไปเมื้อกี้ (CTRL+Z), Tv เปิด
สั่งเปลี่ยนช่อง, เปลี่ยนช่อง Tv เป็นช่อง 72
สั่งเปลี่ยนช่อง, เปลี่ยนช่อง Tv เป็นช่อง 55
ยกเลิกที่สั่งไปเมื้อกี้ (CTRL+Z), เปลี่ยนช่อง Tv เป็นช่อง 72
ทำซ้ำ (CTRL+Y), เปลี่ยนช่อง Tv เป็นช่อง 55
```

# Credit
https://refactoring.guru  
You can buy his book by click the image below.  
[![img](https://refactoring.guru/images/patterns/book/web-cover-en.png)](https://refactoring.guru/design-patterns/book#buy-now)  
