# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Хамоян Максим Ираклиевич
- РИ210946
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

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
Познакомиться с основными понятиями перцептрона.

## Задание 1

В проекте Unity реализовать перцептрон, который умеет производить вычисления:
OR, AND, NAND, XOR - | дать комментарии о корректности работы.

- OR. Задание было выполнено на лекции. Можно увидеть сразу рабочий код с таблицей истинности:

![Alt text](https://github.com/Maksimyska/DA-in-GameDev-lab1/blob/main/%D0%9C%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%20%D0%BB%D0%B0%D0%B1%D0%B0%204/Screen/first.jpg)

- And:

![Alt text](https://github.com/Maksimyska/DA-in-GameDev-lab1/blob/main/%D0%9C%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%20%D0%BB%D0%B0%D0%B1%D0%B0%204/Screen/and.jpg)

- NAnd

![Alt text](https://github.com/Maksimyska/DA-in-GameDev-lab1/blob/main/%D0%9C%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%20%D0%BB%D0%B0%D0%B1%D0%B0%204/Screen/nand.jpg)

- XOR

![Alt text](https://github.com/Maksimyska/DA-in-GameDev-lab1/blob/main/%D0%9C%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%20%D0%BB%D0%B0%D0%B1%D0%B0%204/Screen/xor.jpg)


## Задание 2
### Пошагово выполнить каждый пункт раздела "ход работы" с описанием и примерами реализации задач
Ход работы:
- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.

Для начала я провел подготовку данных для работы с алгоритмом линейной регрессии(перенес данные из шаблона и остальные математические функции):

```py

In [ ]:
#Import the required modules, numpy for calculation, and Matplotlib for drawing
import numpy as np
import matplotlib.pyplot as plt
#This code is for jupyter Notebook only
%matplotlib inline

# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)

#Show the effect of a scatter plot
plt.scatter(x,y)

```

- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.

Функции iterate и optimize связаны между собой, но как? В функции optimize выполняется специальный алгоритм, с помощью которого мы можем обучить модель строить регрессию. Для этого необходимо, чтобы алгоритм повторялся много раз, количество передается в функцию iterate.

```py
def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * (prediction - y).sum()
    a = a - Lr * da
    b = b - Lr * db
    return a, b


def iterated(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b
```

	Дальше началась итерация. Я выполнил, как и в примере, 6 итераций: первую, вторую, третью, четвертую, пятую и десятитысячную и получил следующие результаты:

```py
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.001

# Step 1
a, b = iterated(a, b, x, y, 1)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

# Step 2
a, b = iterated(a, b, x, y, 2)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

# Step 3
a, b = iterated(a, b, x, y, 3)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

# Step 4
a, b = iterated(a, b, x, y, 4)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

# Step 5
a, b = iterated(a, b, x, y, 5)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)

# Step 6
a, b = iterated(a, b, x, y, 10000)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```
После выполнения итерации получил следующие результаты:
[0.51990082]
[0.58107787]
[0.52199882] [0.58113827] 2631.7484457298474
[0.52618214] [0.58125882] 2622.9998978939293
[0.53242558] [0.58143905] 2610.008638494455
[0.54069162] [0.58167822] 2592.929591540631
[0.55093086] [0.58197539] 2571.96449287189
[1.55791862] [0.80173289] 1538.5993470035482

	Можно сделать вывод, что чем больше итераций, тем меньше потерь loss, от сюда можно ответить на вопрос, поставленный в задаче.  Должна ли величина loss стремиться к 0 при изменении исходных данных? Конечно да! Loss – величина, которая показывает потери. Чем выше кол-во итераций, тем меньше значения этой величины(первый скрин – одни значения, второй – другие)



## Задание 3
### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.

Я, честно говоря, не очень силен в регрессии и в подобных алгоритмах, но мой лучший друг – интернет, очень силен и могущественен. Полистав пару страничек, я понял, что для данного алгоритма необходим параметр – скорость обучения и переменная Lr как раз подходит для этого. Добавив в функцию iterate эту переменную теперь, можно передавать скорость обучения прямо через optimize



## Выводы

Абзац умных слов о том, что было сделано и что было узнано.

Извините, пожалуйста, за мою тупость, но с гитхабом у меня плохая дружба и мне тяжело было понять что от меня требуют. Надеюсь сейчас я сделал все правильно!
Выводы:
После выполнения данной ЛР можно сделать пару выводов и подвести несколько итогов:
	•Чем больше количество итераций, тем выше самообучаемость алгоритма
	•Чем выше самообучаемость алгоритма, тем более высокие точности расчетов при прогнозах. В современном мире это очень полезно во многих сферах жизни


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
