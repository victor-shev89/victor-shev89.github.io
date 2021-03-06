---
layout: post
title:  "Сортировка списка по ключу в Python (sorted и параметр key). Что такое ключ, и как это работает"
date:   2020-10-02 12:00:00 +0300
image: 2020/10/05/main_img_20201005.jpg
tags:   python tutorial sorted
--- 
Зачем этот пост? Стандартный параметр в стандартной функции, о котором написано сотни статей и примеров. Только понимание этого инструмента ко мне пришло после прочтения небольшого лирического отступления в теме о последовательностях в книге [*Р. Лучано «Python. К вершинам мастерства»*](https://www.goodreads.com/book/show/22800567-fluent-python?from_search=true&from_srp=true&qid=Iuleq7hVyU&rank=1). Если коротко, мысль следующая: использование функции в качестве параметра key эффективно, потому что вызывается только один раз для каждого элемента последовательности. В общем-то можно на этом и закончить. Однако, я вспомнил про описание лямбда функций в книге [*Д. Бейдера «Чистый Python»*](https://www.goodreads.com/book/show/36990732-python-tricks?ac=1&from_search=true&qid=1kVCxmIz9d&rank=1), в которой указано, что наиболее удачный и часто используемый вариант применения лямбд — это сортировка по альтернативному ключу. Хороший повод разобрать применение и ключей при сортировки и лямбд. Если нет понимания, как работает инструмент, что остается? «Копипастить» со `stackoverflow` без возможности гибко использовать мощь стандартного инструмента языка. Если Вам, как и мне когда-то, сложно понять, что написано, давайте нарисуем!  

Предположим у нас есть список с именами изображений:
```python
>>> list_img = ['img_0', 'img_01', 'img_2', 'img_11', 'img_111', 'img_3']
```
Так как они все одного типа (str), возможность отсортировать сохраняется. Применим функцию `sorted`:
```python
>>> list_img = sorted(list_img)
>>> list_img
	['img_0', 'img_01', 'img_11', 'img_111', 'img_2', 'img_3']
```
Возможно, Вы ожидали такого вывода, если нет — почитайте о сортировке строк. Если вы хотели бы, что бы img_2 был перед img_11, а img_111 был в самом конце, то строки сортируются по первому различному символу, например список:
```python
>>> list_img = ['img_0', 'amg_01', 'img_2', 'img_11', 'img_111', 'img_3']
```
будет отсортирован следующим образом:
```python
>>> list_img = sorted(list_img)
>>> list_img
	['amg_01', 'img_0', 'img_11', 'img_111', 'img_2', 'img_3']
```
Допустим. А как в таком случае получить ожидаемый результат сортировки по номеру в названии изображения? Надо просто применить ключ!

Вернемся к списку:
```python
>>> list_img = ['img_0', 'img_01', 'img_2', 'img_11', 'img_111', 'img_3']

>>> list_img = sorted(list_img, key=number_in_image_title)
>>> list_img
	['img_0', 'img_01', 'img_2', 'img_3', 'img_11', 'img_111']
```
Выглядит логично и ожидаемо, например, с точки зрения порядка обработки этих изображений в будущем. Что в данном случае является ключом  `number_in_image_title` (номер в названии изображения)? Это функция, которая принимает один аргумент — название изображения, обрабатывает его и возвращает номер, который содержится в названии. Например, такая функция:
```python
>>> def number_in_image_title(image_title):
>>>    digit_str = ''
>>>    for character in image_title:
>>>        if character.isdigit():
>>>            digit_str += character
>>>    return int(digit_str)
```
Воспользуемся визуализацией процессов на сайте [pythontutor](http://pythontutor.com/). 

[Ссылка на данный код](http://pythontutor.com/visualize.html#code=def%20number_in_image_title%28image_title%29%3A%0A%20%20%20%20digit_str%20%3D%20''%0A%20%20%20%20for%20character%20in%20image_title%3A%0A%20%20%20%20%20%20%20%20if%20character.isdigit%28%29%3A%0A%20%20%20%20%20%20%20%20%20%20%20%20digit_str%20%2B%3D%20character%0A%20%20%20%20return%20int%28digit_str%29%0A%0Alist_img%20%3D%20%5B'img_0',%20'img_01',%20'img_2',%20'img_11',%20'img_111',%20'img_3'%5D%0Alist_img%20%3D%20sorted%28list_img,%20key%3Dnumber_in_image_title%29%0Aprint%28list_img%29&cumulative=false&curInstr=1&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false).

Когда мы дошли до строки с сортировкой списка:

![]({{ site.baseurl }}/images/2020/10/05/1.png)  

Мы имеем два объекта в глобальной области: функцию `number_in_image_title` и список `list_img`.

![]({{ site.baseurl }}/images/2020/10/05/2.png)  

Далее, функция `sorted`, вызывает объект, который указан в качестве аргумента параметра key -  `number_in_image_title`, а в качестве аргумента для `number_in_image_title` использует первый элемент итерируемого списка (в данном случае `„img_0“`), переданного для сортировки (`list_img`).

![]({{ site.baseurl }}/images/2020/10/05/3.png)

![]({{ site.baseurl }}/images/2020/10/05/4.png)  

Проходя дальше по циклу (3-5 строчка кода) мы проверяем, является ли каждый символ цифрой, и если является конкатенируем с пустой строкой `digit_str`.

![]({{ site.baseurl }}/images/2020/10/05/5_1.png)![]({{ site.baseurl }}/images/2020/10/05/5_2.png)![]({{ site.baseurl }}/images/2020/10/05/5_3.png)![]({{ site.baseurl }}/images/2020/10/05/5_4.png)![]({{ site.baseurl }}/images/2020/10/05/5_5.png)![]({{ site.baseurl }}/images/2020/10/05/5_6.png)   
Функция  `number_in_image_title` возвращает нам цифру 0. Следующим шагом функция `sorted` вызовет  `number_in_image_title` с аргументом `«img_01»`, `digit_str` будет `«01»`, а вернет функция `1`:  
![]({{ site.baseurl }}/images/2020/10/05/6.png)  

И так далее, пока все элементы списка не будут обработаны функцией, указанной как аргумент параметра `key`.
Что получается? Без ключа мы имеем список:
```python
['img_0', 'img_01', 'img_2', 'img_11', 'img_111', 'img_3']
```
который сортируется, согласно правилам сортировки строк. С ключом мы имеем тот же список, но сортируем не его, а фактически мы сортируем возвращаемые значения от функции `key`:
```python
[0, 1, 2, 11, 111, 3]
```
Новый список связан с оригинальным индексами позиций элементов.
```python
['img_0', 		'img_01', 	 'img_2', 	'img_11', 	'img_111', 		'img_3'	  ]
    |             |             |          |        	|              |
[   0,            1,            2,         11,         111,            3      ]
```
Теперь, если мы должны поместить цифру 3 в позицию 3-его элемента при сортировке нового списка, то `«img_3»`, соответственно, будет помещен в позицию 3-его элемента в конечном отсортированном списке. Аналогично и с остальными элементами оригинального и нового списка.

В итоге следующий код:
```python
def number_in_image_title(image_title):
    digit_str = ''
    for character in image_title:
        if character.isdigit():
            digit_str += character
    return int(digit_str)

list_img = ['img_0', 'img_01', 'img_2', 'img_11', 'img_111', 'img_3']
list_img = sorted(list_img, key=number_in_image_title)
print(list_img)
```
даст нам ожидаемый, с точки зрения сортировки по номеру изображения результат:
```python
['img_0', 'img_01', 'img_2', 'img_3', 'img_11', 'img_111']
```
Таким образом, мы можем отсортировать итерируемый объект, с использованием любой логики сортировки, используя ключ, которым является функция, принимающая один аргумент и этот аргумент — элемент итерируемого списка.

Хорошо, но где же тут лямбда функция? В Python лямбда функции — не именованные однострочные функции. Есть ли у Вас опыт решения многоэтапных задач в одну строчку? Если Вы не понимаете о чем речь, посетите эти страницы: [Powerful Python One-Liners](https://wiki.python.org/moin/Powerful%20Python%20One-Liners), [One-lined Python](http://www.onelinerizer.com/). А теперь посмотрите на код функции  `number_in_image_title`. Понимаете ли Вы как можно решить задачу определения числа/цифры в строке (например, `«img_0»`) в одну строчку? Если да, тогда у Вас нет с этим проблем, просто оберните эту логику в лямбда. Если нет, то вот возможное решение:

```python
>>> int(''.join([character for character in „img_0“ if character.isdigit()]))
out: 0
```

Функция сортировки с использованием лябда будет выглядеть следующим образом:

```python
list_img = sorted(list_img, key=lambda image_title: int(''.join([character for 		character in image_title if character.isdigit()])))
```

Вместо 6 строк кода и 1 функции мы использовали 0 строк кода, 0 функций, если не считать лямбда, которую мы объявили и сразу применили в качестве аргумента для параметра `key`! Это мощно! Но возможно трудно читаемо, особенно для новичков. 

Можете быть уверены, что и результат и процесс ничем не отличается от варианта с функцией `number_in_image_title`. А впрочем, не стоит верить на слово, [вот код для этого варианта](http://pythontutor.com/visualize.html#code=list_img%20%3D%20%5B'img_0',%20'img_01',%20'img_2',%20'img_11',%20'img_111',%20'img_3'%5D%0Alist_img%20%3D%20sorted%28list_img,%20key%3Dlambda%20image_title%3A%20int%28''.join%28%5Bcharacter%20for%20character%20in%20image_title%20if%20character.isdigit%28%29%5D%29%29%29%0Aprint%28list_img%29&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false) (плюс еще и с визуализацией!), посмотрите и проверьте сами.

Логика та же, только вместо названия функции  `number_in_image_title` у нас `lambda`, с тем же параметром `image_title`, принимающая в качестве аргумента элемент итерируемого списка  `list_img`. `join` — метод строки, объединяет все цифры из конкретного элемента `list_img`, который передан в lambda как `image_title`. Цифры мы получаем как результат работы генератора списков (List Comprehension). Ну вот и всё! Практикуйтесь, читайте чужой код, не бойтесь нового и пытайтесь понять смысл выражений. 



Читай:

1. [Is it possible to write obfuscated one-liners in Python?](https://docs.python.org/3/faq/programming.html#is-it-possible-to-write-obfuscated-one-liners-in-python)
