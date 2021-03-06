---
layout: post
title:  "Python. Псевдонимы объектов. Как сломать объект, если он представился другим именем"
date:   2020-10-07 12:00:00 +0300
image:  2020/10/07/main_img_20201007.jpg
tags:   python tutorial alias
---

Каждый более менее весомый проект разрастается до размеров, когда следить за переменными и созданными объектами сохраняя их в памяти программиста невозможно. Как был создан тот или иной объект уже не вспомнить. Но вдруг, меняя содержимое одной переменной, мы получаем неожиданное изменение другой. И эту ситуацию проще выловить, если программа упала. Но если она не сломалась, а продолжает неправильно работать, могут быть неприятные последствия.

#### Один объект много имен

При создании переменной, мы создаем в памяти какой-то объект и связываем его с именем. Например:

```python
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = list_of_int
```

Тут мы создали объект  в памяти `[1, 2, 3] `  и дали ему название`list_of_int`. Далее мы присвоили объекту под названием `list_of_int` ещё одно название - `same_list_of_int`. Теперь у нас есть два названия для одного объекта `[1, 2, 3] `, и обращаться к нему и изменять его мы можем используя любое из них. Стоит твердо уяснить, что изменяя `list_of_int` мы не меняем `same_list_of_int`, мы меняем объект `[1, 2, 3] `, а его новое состояние мы можем увидеть используя любое из его имен. 

![]({{ site.baseurl }}/images/2020/10/07/1.png)  

------

*Это как будто у вас есть кошка, которую вы зовёте `Муркой`, а ваш сосед, не зная об этом, зовет её `Мурлыкой`. Но если эта кошка подерется с котом и сломает хвост, он будет сломан и у `Мурки` и у  `Мурлыки`. Так что будьте осторожны и обеспечьте безопасность своему "кот(д)у".*

------

Давайте изменим исходный объект, обратившись к нему по обоим из его ''псевдонимов":

```python
>>> list_of_int.append(777)
>>> same_list_of_int[0] = 1000
>>> print(f"list_of_int: {list_of_int}, same_list_of_int: {same_list_of_int} ")
out: list_of_int: [1000, 2, 3, 777], same_list_of_int: [1000, 2, 3, 777] 
```

Получим изменение объекта, доступное по всем его именам.

![]({{ site.baseurl }}/images/2020/10/07/2.png)  

Интересно, что присваивая новое имя объекту `[1, 2, 3] ` через его существующее имя, мы создаем его "псевдоним", а присваивая новому имени идентичный (по содержимому) объект, мы создаем новый объект:

```python
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = [1, 2, 3]
>>> print(f"id list_of_int: {id(list_of_int)}, id same_list_of_int: {id(same_list_of_int)}")
out: id list_of_int: 140349047025600, id same_list_of_int: 140349047638208
```

![]({{ site.baseurl }}/images/2020/10/07/3.png)  

Понятно, что если этот подход используется в Python, то это является оптимальным для языка. Но это может иметь неожиданные эффекты, если не понимать как работают ссылки и создаются/копируются объекты. 

Что делать, если есть некоторый объект, который должен быть копией другого, и его надо изменять, а исходный должен быть в целости и сохранности? Самый простой вариант - создать его заново, например, скопировав его или применив метод list() к исходной последовательности:

```python
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = list_of_int.copy()
>>> print(f"id list_of_int: {id(list_of_int)}, id same_list_of_int: {id(same_list_of_int)}")
out: id list_of_int: 140349047639488, id same_list_of_int: 140349047078336

>>> same_list_of_int = list(list_of_int)
>>> print(f"id list_of_int: {id(list_of_int)}, id same_list_of_int: {id(same_list_of_int)}")
out: id list_of_int: 140349047085696, id same_list_of_int: 140349047173696
```

Как же понять, один передо мной объект с разными псевдонимами или это разные объекты. Объекты можно сравнить на равенство, а можно проверить на идентичность. Равенство скажет, что объекты и их содержимое эквиваленты, а идентичность скажет на один ли объект они ссылаются:

```python
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = list_of_int.copy()
>>> same_list_of_int == list_of_int
out: True
>>> same_list_of_int is list_of_int
out: False
```

И последнее. Любое переназначение другому объекту существующего имени ведет к отвязыванию от текущего объекта и привязыванию к новому:

```python
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = list_of_int
>>> list_of_int = (1, 3, 4)
>>> print(f"list_of_int: {list_of_int}, same_list_of_int: {same_list_of_int} ")
out: list_of_int: (1, 3, 4), same_list_of_int: [1, 2, 3] 
            
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = list_of_int
>>> list_of_int = [1, 2, 3]
>>> print(f"list_of_int is same_list_of_int: {list_of_int is same_list_of_int}")
out: list_of_int is same_list_of_int: False
        
>>> list_of_int = [1, 2, 3]
>>> same_list_of_int = list_of_int
>>> same_list_of_int = same_list_of_int*2
>>> print(f"list_of_int: {list_of_int}, same_list_of_int: {same_list_of_int} ")
out: list_of_int: [1, 2, 3], same_list_of_int: [1, 2, 3, 1, 2, 3]
```
