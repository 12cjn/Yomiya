- [LeaningDjango4.0](src/myknowledge/Django/LeaningDjango4.0.md)
- [RDF API 指南](https://12cjn.github.io/django-rest-framework-api-guide/#/)

> 顺序图-sequenceDiagram

基本语法：[Actor][Arrow][Actor]:Message text，Actor表示角色的意思。其中Arrow有->,–>,->>,–>>,-X,–X。

Styling of a sequence diagram is done by defining a number of css classes. During rendering these classes are extracted from the file located at src/themes/sequence.scss：序列图的样式是通过定义多个CSS类来完成的。在渲染期间，这些类是从src / themes / sequence.scss中的文件中提取的。

sequenceDiagram
	%% 自动编号
	autonumber
	%% 定义参与者并取别名，aliases：别名
        participant A as Aly
        participant B as Bob
        participant C as CofCai
        %% 便签说明
        Note left of A: 只复习了一部分
        Note right of B: 没复习
        Note over A,B: are contacting
        
        A->>B: 明天是要考试吗？
        B-->>A: 好像是的！
        
        %% 显示并行发生的动作，parallel：平行
        %% par [action1]
        rect rgb(0, 255, 255)
            par askA
                C -->> A:你复习好了吗？
            and askB
                C -->> B:你复习好了吗？
            and self
                C ->>C:我还没准备复习......
            end
        end
        
        %% 背景高亮，提供一个有颜色的背景矩形
        rect rgb(255, 255, 0)
            loop 自问/Every min
            %% <br/>可以换行
            C ->> C:我什么时候<br/>开始复习呢？
            end
        end
        
        %% 可选择路径
        rect rgb(200, 100, 100)
            alt is good
                A ->> C:复习了一点
            else is common
                B ->> C:我也是
            end
            %% 没有else时可以提供默认的opt
            opt Extra response
                C ->> C:你们怎么不回答我
            end
        end

> 类图-class diagrams

用于类之间的所属关系表达

classDiagram
	%% Duck继承自Animal
        Animal <|-- Duck
        Animal <|-- Fish
        Animal <|-- Zebra
        %% +即public；-即private；#即protected；~即Package/Internal
        Animal : +int age
        Animal : +String gender
        %% 返回值类型，在括号后面加，记得要有一个空格
        Animal: +isMammal() bool
        Animal: +mate()
        %% Duck有Animal的属性和方法
        class Duck{
            +String beakColor
            +swim()
            +quack()
        }
        class Fish{
            -int sizeInFeet
            -canEat()
        }
        class Zebra{
            +bool is_wild
            +run(speed)
        }
        Duck <|-- yellowDuck
        class yellowDuck{
        	-string color
        	-int size
        }

> 类之间的关系

classDiagram
	%% [classA][Arrow][ClassB]:LabelText
        classA --|> classB : Inheritance
        classC --* classD : Composition
        classE --o classF : Aggregation
        classG --> classH : Association
        classI -- classJ : Link(Solid)
        classK ..> classL : Dependency
        classM ..|> classN : Realization
        classO .. classP : Link(Dashed)

> 类注释

- <<Interface>> To represent an Interface class：接口类
- <<abstract>> To represent an abstract class：抽象类
- <<Service>> To represent a service class：服务类
- <<enumeration>> To represent an enum：枚举类

classDiagram
    class Shape {
        <<interface>>
        noOfVertices
        -len
        -high
        drawPoint()
        drawLine()
        drawRctangle()
    }
    class Color {
        <<enumeration>>
        RED
        BLUE
        GREEN
        WHITE
        BLACK
    }
    class type {
    	<<abstract>>
    	Animal
    }
