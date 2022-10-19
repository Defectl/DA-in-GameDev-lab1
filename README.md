# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Латынцева Елена Сергеевна
- РИ210930
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
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity. При выполнении задания можно использовать видео-материалы и исходные данные, предоставленные преподавателями курса.

Ход работы

- При этапе подготовки необходимо скачать папку с ML-агентом.
- Необходимо в новом 3D проекте Unity добавить .json – файлы:

![image](https://user-images.githubusercontent.com/101496751/196680373-ca879ba2-994c-4612-b547-07a7e8cf4be5.png)

- С помощью Anaconda Promt нужно активировать новый ML-агент и скачать новые библиотеки: mlagents 0.28.0, torch 1.7.1

![image](https://user-images.githubusercontent.com/101496751/196694228-d5a4402b-d682-4572-8a0e-193ae6cd4e0d.png)
![image](https://user-images.githubusercontent.com/101496751/196716369-05f9c958-3100-42cf-954b-11eb0de8ff09.png)
![image](https://user-images.githubusercontent.com/101496751/196697767-87c98ae1-1b13-4895-b160-c111b900a834.png)

- Далее нужно создать TargetArea, в которой находится плоскость, шар и куб. Также нужно создать C# скрипт-файл, добавить в него код, опубликованный в материалах для работы, и привязать его к сфере:

![image](https://user-images.githubusercontent.com/101496751/196687103-29c67ad6-5e78-4ce0-88b1-6567f3582a2c.png)

Код из файла представлен ниже:

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
- Объекту RollerAgent нужно добавить компоненты Rigidbody, Decision Requester, Behavior Parameters с такими настройками:
![image](https://user-images.githubusercontent.com/101496751/196690522-1dbaea53-ddeb-4348-b8b0-6791e7ed3310.png)
![image](https://user-images.githubusercontent.com/101496751/196690558-79957dfe-f758-4558-8984-9ad44a75df45.png)

- После добавления файла конфигурации нейронной сети в корень проекта, нужно запустить работу ml-агента:

![image](https://user-images.githubusercontent.com/101496751/196716130-0e3e659f-b09f-4a37-85e6-e6b58f94ce08.png)

- Нужно вернуться в Unity и проверить работу ML-агента. При запуске программы в консоли выводится такой код:

![image](https://user-images.githubusercontent.com/101496751/196713485-81448754-2177-404a-8709-4ce39f99e725.png)

И у шара появилось такое  поведение:

![Untitled](https://user-images.githubusercontent.com/101496751/196725774-30aa975a-27b5-4eab-86b0-5705cce9d502.gif)


- Симуляция сцены с тремя моделями:

- Симуляция сцены с девятью моделями:

- Симуляция сцены с двадцатьюсемью моделями:

- Работа модели после завершения обучения (для этого надо перенести файл RollerBall.onnx из результатов в assets, а потом перенести его в данное поле):

![image](https://user-images.githubusercontent.com/101496751/196722873-0ebd3c2b-122e-4051-8086-7f10cc862f6b.png)




## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.


## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости.


## Выводы


| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| Google.colab | [plugins/googlecolab/README.md][PlGc] |
| Google Sheets | [plugins/googlesheets/README.md][PlGS] |
| Google Console | [plugins/googleconsole/README.md][PlGC] |
| PyCharm | [plugins/pycharm/README.md][PlPC] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
