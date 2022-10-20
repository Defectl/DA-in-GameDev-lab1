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

- С помощью Anaconda Promt нужно активировать новый ML-агент и скачать новые библиотеки: mlagents 0.28.0, torch 1.7.1. Вводим такие команды:

```py
conda create -n MLAgents python=3.6
conda activate MLAgents
```

![image](https://user-images.githubusercontent.com/101496751/196694228-d5a4402b-d682-4572-8a0e-193ae6cd4e0d.png)

```py
pip install mlagents==0.27.0
```

![image](https://user-images.githubusercontent.com/101496751/196716369-05f9c958-3100-42cf-954b-11eb0de8ff09.png)

```py
pip install torch~=1.7.1 -f https://download.pytorch.org/whl/torch_stable.html
```

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

- Симуляция сцены с 3-мя моделями:

![Untitled (2)](https://user-images.githubusercontent.com/101496751/196727144-62308cfa-5dfb-4c2e-879f-2ca4978ec37f.gif)


- Симуляция сцены с 9-ю моделями:

![Untitled (5)](https://user-images.githubusercontent.com/101496751/196728351-27ac38fd-4ff7-4551-8b29-84d3355534ad.gif)

- Симуляция сцены с 27-ю моделями:

![Untitled (7)](https://user-images.githubusercontent.com/101496751/196729165-5896f48b-c396-4975-98bf-dfc7b84bc6bf.gif)

- Работа модели после завершения обучения (для этого надо перенести файл RollerBall.onnx из результатов в assets, а потом перенести его в данное поле):

![image](https://user-images.githubusercontent.com/101496751/196722873-0ebd3c2b-122e-4051-8086-7f10cc862f6b.png)

![Untitled (2)](https://user-images.githubusercontent.com/101496751/196930388-58b0a52e-8685-42e8-ba27-57c2dfc71bf2.gif)

Выводы: При увелечении количества агентов в Unity (копий модели), модель быстрее обучается, то есть достигает более высоких показателей точности за меньше времени итераций.




## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.

Код из файла конфигурации нейронной сети:

```py
behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```

### behaviors
Описание поведения объекта
    
### RollerBall
Имя объекта
    
### trainer_type: ppo
Тип обучения с поощрением
    
### hyperparameters
Гиперпараметры   
    
### batch_size: 10
Количество опытов на каждой итерации градиентного спуска. Это всегда должно быть в несколько раз меньше, чем buffer_size. Если вы используете непрерывные действия, это значение должно быть большим (порядка 1000). Если вы используете только дискретные действия, это значение должно быть меньше (порядка 10 секунд).
    
### buffer_size: 100
Так как у нас trainer_type: ppo то данный параметр - это количество опыта, который необходимо собрать перед обновлением модели политики. Соответствует тому, сколько опыта должно быть собрано, прежде чем мы приступим к какому-либо изучению или обновлению модели. Это должно быть в несколько раз больше, чем batch_size. Обычно больший размер буфера соответствует более стабильным обновлениям обучения.
    
### learning_rate: 3.0e-4
Начальная скорость обучения для градиентного спуска. Соответствует силе каждого шага обновления градиентного спуска. Обычно это значение следует уменьшить, если тренировка нестабильна, а вознаграждение постоянно не увеличивается. У нас стоит дефолтное значение. 
    
### beta: 5.0e-4
Сила регуляризации энтропии, которая делает политику "более случайной". Это гарантирует, что агенты должным образом исследуют пространство действий во время обучения. Увеличение этого параметра обеспечит выполнение большего количества случайных действий. Это должно быть скорректировано таким образом, чтобы энтропия (измеряемая с помощью TensorBoard) медленно уменьшалась вместе с увеличением вознаграждения. 
    
### epsilon: 0.2
Влияет на то, насколько быстро политика может развиваться во время обучения. Соответствует допустимому порогу расхождения между старой и новой политиками при обновлении с градиентным спуском. Установка этого малого значения приведет к более стабильным обновлениям, но также замедлит процесс обучения.
    
### lambd: 0.99
Параметр регуляризации (лямбда), используемый при расчете обобщенной оценки преимущества (GAE). Это можно рассматривать как то, насколько агент полагается на свою текущую оценку стоимости при расчете обновленной оценки стоимости. Низкие значения соответствуют тому, что вы больше полагаетесь на текущую оценку стоимости (что может быть большим смещением), а высокие значения соответствуют тому, что вы больше полагаетесь на фактические вознаграждения, полученные в окружающей среде (что может быть большим отклонением). Параметр обеспечивает компромисс между ними, и правильное значение может привести к более стабильному процессу обучения. 
    
### num_epoch: 3
Количество проходов через буфер опыта при выполнении оптимизации градиентного спуска.Чем больше размер пакета, тем больше это допустимо сделать. Уменьшение этого параметра обеспечит более стабильные обновления за счет более медленного обучения.    
    
### learning_rate_schedule: linear
Имя объекта    
    
### network_settings:
Имя объекта    
    
### normalize: false
Имя объекта   
    
### hidden_units: 128
Имя объекта
    
### num_layers: 2
Имя объекта    
    
### reward_signals:
Имя объекта    
    
### extrinsic:
Имя объекта    
    
### gamma: 0.99
Имя объекта    
    
### strength: 1.0
Имя объекта    
    
### max_steps: 500000
Имя объекта    
        
### time_horizon: 64
Имя объекта    
    
### summary_freq: 10000
Имя объекта        
    
    
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
