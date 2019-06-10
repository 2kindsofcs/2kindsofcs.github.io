---
layout: post
title: "getter와 setter"
slug: "been-ages"
tags: [TIL]
---

* 파이썬은 변수, 메소드에 대해 privacy option을 제공하지 않는다.
* getter와 setter를 보다 이쁘게(?) 사용하기 위해 파이썬에는 [property]가 있다.
* property는 일종의 syntactic sugar라고 할 수 있다. 보다 깔끔하고 편하게 gettr 와 setter를 쓸 수 있게 설탕을 친 것이다. 

{% raw %}
```python
class Test:
    def __init__(self,x):
        self.x = x          # 아래 작성된 x.setter 이하 내용을 실행시킨다
    
    @property
    def x(self):            # x.setter 이하 내용에 의해 정해진 self.__x를 리턴
        return self.__x

    @x.setter 
    def x(self, x):
        if x<0:
            self.__x = 0
        elif x > 1000:
            self.__x = 1000
        else:
            self.__ x = x    
```
{% endraw %}


Methods like getters and setters let us dictate the rules under which class variables can be accessed and changed. Even if we are writing a class where we don't mind that the variables are accessed and changed haphazardly, we still might want to know about those changes.

Getter: A common type of method in writing classes that returns the value of a variable contained within the class. They are commonly used to allow other processing to occur whenever the variable is accessed, like logging.

Setter: A common type of method in writing classes that sets a variable contained within the class to a new value. They are commonly used 
to allow other processing to occur whenever the variable is changed, like logging.


Getters and setters let us 
* log whenever a variable's value is accessed or changed.
* prevent unauthorized access to instance variables' values.
* build customized ways for modifying or retrieving instance variables' values.

[property]: https://www.python-course.eu/python3_properties.php "property 예제"