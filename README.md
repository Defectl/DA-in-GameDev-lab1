# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Латынцева Елена Сергеевна
- РИ210930
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Ознакомиться с основными операторами языка Python на примере реализации линейной регрессии.

## Задание 1
### Написать программы Hello, World на Python и Unity.
Ход работы:
- Для Python в отчете привести скриншоты с демонстрацией сохранения документа google.colab на свой диск с запуском программы, выводящей сообщение Hello, World.

Код на Python (файл с кодом лежит в папке [Laboratory work 1](https://github.com/Defectl/DA-in-GameDev-lab1/tree/main/Laboratory%20work%201) с назвзанием [Work1](https://github.com/Defectl/DA-in-GameDev-lab1/blob/main/Laboratory%20work%201/_Work1.ipynb));

```py
print('Hello, World')
```

скриншот запуска программы в google.colab;

![Снимок экрана 2022-09-20 151950](https://user-images.githubusercontent.com/101496751/191535837-03582682-ecdf-4a1d-8072-62dcd9e85cb0.png)

скриншот сохранения документа google.colab на [свой диск](https://drive.google.com/drive/folders/1ssdcaV8fwvZrdHcvlvRjshRf9WTsFhwD?usp=sharing);

![Снимок экрана 2022-09-20 152324](https://user-images.githubusercontent.com/101496751/191536234-8a4fe9c7-745f-46e8-b06f-988bbdd7ff40.png)


- Для Unity в отчете привести скриншоты вывода сообщения Hello, World в консоль.

Код на Unity(файл с кодом лежит в папке [Laboratory work 1](https://github.com/Defectl/DA-in-GameDev-lab1/tree/main/Laboratory%20work%201) с назвзанием [HelloWorld](https://github.com/Defectl/DA-in-GameDev-lab1/blob/main/Laboratory%20work%201/HelloWorld.cs)).

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class HelloWorld : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        print("Hello, World");       
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```

Для того, чтобы код заработал, нужно было привязать скрипт с данным код к какому-либо объекту и запустить сцену.

Скриншот вывода сообщения Hello, World в консоль.

![Снимок экрана 2022-09-20 155924](https://user-images.githubusercontent.com/101496751/191539701-e982d97e-292f-4e8a-8848-8e6d7d4717a1.png)


## Задание 2
### В разделе "ход работы" пошагово выполнить каждый пункт с описанием и примером реализации задачи по теме лабораторной работы.

Ход работы.
1. Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.

Код на Python:

```py
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)
```
Пояснение. Сначала нужно импортировать необходимые библиотеки для работы: 'numpy' для вычислений и 'matplotlib' для отрисовки. В следующих строках мы задаем случайным образом данные и приводим их к формату массива. В последней строке мы отрисовываем наши данные на графике. Скриншот вывода графика:

![Снимок экрана 2022-09-20 161224](https://user-images.githubusercontent.com/101496751/191751554-cb9a1fb1-aa98-4130-a3d3-13d80d4c4a48.png)

2. Определите связанные функции. 
Функция модели: определяет модель линейной регрессии a * x + b. 
```py
def model(a, b, x):
  return a*x + b
```

Функция потерь: функция потерь среднеквадратичной ошибки. 
```py
def loss_function(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  return (0.5 / num) * (np.square(prediction - y)).sum()
```

Функция оптимизации: метод градиентного спуска для нахождения частных производных a и b.
```py
def optimize(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  #Обновление значений a и b, поиском частных производных функций потерь на a и b
  da = (1.0 / num) * ((prediction - y) * x).sum()
  db = (1.0 / num) * ((prediction - y).sum())
  a = a - Lr * da
  b = b - Lr * db
  return a, b
```

Функция итерации: возвращает значения a и b.
```py
def iterate(a, b, x, y, times):
  for i in range(times):
    a, b= optimize(a, b, x, y)
  return a, b
```

3. Начать итерацию

Шаг 1. Инициализация и модель итеративной оптимизации
```py
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a, b = iterate(a, b, x, y, 1)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Код и график:
![Снимок экрана 2022-09-20 175055](https://user-images.githubusercontent.com/101496751/191759373-8a992122-769b-446e-bf29-bb26ef9aae21.png)

Шаг 2. На второй итерации отображаются значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a, b = iterate(a, b, x, y, 2)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Код и график:
![Снимок экрана 2022-09-20 175413](https://user-images.githubusercontent.com/101496751/191760375-88a5014a-fc8f-4bcb-934a-2cc1f4e00b7e.png)

Шаг 3. Третья итерация показывает значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a, b = iterate(a, b, x, y, 3)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Код и график:
![Снимок экрана 2022-09-20 175442](https://user-images.githubusercontent.com/101496751/191760671-6efc5b42-3a84-480e-ae91-bb2ba6586698.png)

Шаг 4. На четвертой итерации отображаются значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a, b = iterate(a, b, x, y, 4)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Код и график:
![Снимок экрана 2022-09-20 175528](https://user-images.githubusercontent.com/101496751/191760825-67d3e6b8-0e93-489f-ab2a-b477d1808a8c.png)

Шаг 5. Пятая итерация показывает значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a, b = iterate(a, b, x, y, 5)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Код и график:
![Снимок экрана 2022-09-20 175649](https://user-images.githubusercontent.com/101496751/191760955-e773bd42-d6ab-44b8-983c-f04c79b8bb86.png)

Шаг 6. 10000-я итерация, показывающая значения параметров, значения потерь и эффекты визуализации после итерации.

```py
a, b = iterate(a, b, x, y, 10000)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Код и график:
![Снимок экрана 2022-09-20 180055](https://user-images.githubusercontent.com/101496751/191761192-42e9d407-51dc-44b6-aec5-7732671cf192.png)

Смотря на значения в консоли и графики по итерациям, можно проследить, что значения параметров a и b с каждой итерацией увеличиваются, а вот значения потерь, наоборот, уменьшаются.

*Файл с кодом лежит в папке [Laboratory work 1](https://github.com/Defectl/DA-in-GameDev-lab1/tree/main/Laboratory%20work%201) с назвзанием [Work1.2.](https://github.com/Defectl/DA-in-GameDev-lab1/blob/main/Laboratory%20work%201/Work1.2.ipynb)


## Задание 3
### Изучить код на Python и ответить на вопросы:

- Должна ли величина loss стремиться к нулю при изменении исходных данных? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ.

Для эксперимента я решила уменьшить исходные данные:

```py
x = [1, 4, 9, 15, 19, 23, 22, 12, 21, 26]
x = np.array(x)
y = [3, 5, 10, 27, 18, 33, 9, 11, 14, 13]
y = np.array(y)
```

Произошло изменение: при старых значениях на 10000-ой итерации значение потерь было около 191, теперь же с уменьшенными исходными данными значение потерь всегда около 29.

Скриншоты вызовов итераций:

![image](https://user-images.githubusercontent.com/101496751/191770542-7b60fcca-1c52-48e2-a494-059b8c8cb573.png)
![image](https://user-images.githubusercontent.com/101496751/191770717-b2a1f208-33d6-47b5-a591-015167718932.png)

Можно сделать вывод, что при уменьшении исходных данных уменьшается величина потерь, то есть стремится к нулю.

*Файл с кодом лежит в папке [Laboratory work 1](https://github.com/Defectl/DA-in-GameDev-lab1/tree/main/Laboratory%20work%201) с назвзанием [Work1.3.](https://github.com/Defectl/DA-in-GameDev-lab1/blob/main/Laboratory%20work%201/Work1.3.ipynb)


- Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ван ответ. В качестве эксперимента можете изменить значение параметра.

Для эксперимента я увеличила значение Lr в 100 раз.
Произошли такие изменения со значениями и графиком:

![image](https://user-images.githubusercontent.com/101496751/191778766-07401362-4b4e-4390-b472-c8f79806258a.png)

Разница между значениями при итерациях стала больше, чем со старым значением параметра Lr.

Можно сделать вывод, что параметр Lr отвечает за разницу значений после каждой итерации. Чем больше значение Lr, тем больше разница между значениями.

*Файл с кодом лежит в папке [Laboratory work 1](https://github.com/Defectl/DA-in-GameDev-lab1/tree/main/Laboratory%20work%201) с назвзанием [Work1.3.2.](https://github.com/Defectl/DA-in-GameDev-lab1/blob/main/Laboratory%20work%201/Work1.3.2.ipynb)



## Выводы

Абзац умных слов о том, что было сделано и что было узнано.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
