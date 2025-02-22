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

- С помощью Anaconda Prompt нужно активировать новый ML-агент и скачать новые библиотеки: mlagents 0.28.0 (в моем случае я устанавливала на версию ниже), torch 1.7.1. Вводим такие команды:

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

Выводы: При увелечении количества агентов в Unity (копий модели), модель быстрее обучается, то есть достигает более высоких показателей точности за меньшее время итераций.


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
Определяет, как скорость обучения меняется с течением времени. Для PPO рекомендуется снижать скорость обучения до max_steps, чтобы обучение сходилось более стабильно. Однако в некоторых случаях (например, обучение в течение неизвестного периода времени) эта функция может быть отключена. Для trainer_type: ppo стоит дефолтное значение - linear.
    
### network_settings:
Сетевые установки    
    
### normalize: false
Применяется ли нормализация к входным данным векторного наблюдения. Эта нормализация основана на текущем среднем значении и дисперсии векторного наблюдения. Нормализация может быть полезна в случаях со сложными проблемами непрерывного управления, но может быть вредной при решении более простых задач дискретного управления. В нашем случае стоит дефолтное значение.  
    
### hidden_units: 128
Количество единиц в скрытых слоях нейронной сети. Соответствует количеству единиц в каждом полностью связном слое нейронной сети. Для простых задач, где правильное действие представляет собой простую комбинацию входных данных наблюдения, это должно быть небольшим. Для задач, где действие представляет собой очень сложное взаимодействие между переменными наблюдения, это должно быть больше. Стоит дефолтное значение.
    
### num_layers: 2
Количество скрытых слоев в нейронной сети. Соответствует тому, сколько скрытых слоев присутствует после ввода наблюдения или после кодирования CNN визуального наблюдения. Для простых задач меньшее количество слоев, скорее всего, будет обучаться быстрее и эффективнее. Для более сложных задач управления может потребоваться больше уровней. Стоит дефолтное значение.  
    
### reward_signals:
Этот раздел позволяет задать настройки как для внешних (т.е. основанных на окружающей среде), так и для внутренних сигналов вознаграждения (например, curiosity и GAIL).    
    
### extrinsic:
Эти настройки нужны, чтобы убедиться, что тренировочный прогон включает в себя сигнал вознаграждения, основанный на окружающей среде.
    
### gamma: 0.99
Коэффициент дисконтирования для будущих вознаграждений, поступающих от окружающей среды. Это можно рассматривать как то, насколько далеко в будущем агент должен заботиться о возможных вознаграждениях. В ситуациях, когда агент должен действовать в настоящем, чтобы подготовиться к вознаграждению в отдаленном будущем, это значение должно быть большим. В тех случаях, когда вознаграждение является более немедленным, оно может быть меньше. Должно быть строго меньше 1. 
    
### strength: 1.0
Фактор, на который можно умножить вознаграждение, получаемое от окружающей среды. Типичные диапазоны будут варьироваться в зависимости от сигнала вознаграждения.
    
### max_steps: 500000
Общее количество шагов (т.е. собранных наблюдений и предпринятых действий), которые должны быть выполнены в среде (или во всех средах, если используется несколько параллельно) до завершения процесса обучения. Если у вас есть несколько агентов с одинаковым именем поведения в вашей среде, все шаги, выполняемые этими агентами, будут способствовать одинаковому количеству max_steps. 
        
### time_horizon: 64
Сколько шагов опыта нужно собрать для каждого агента, прежде чем добавлять его в буфер опыта. Когда этот предел достигается до окончания эпизода, оценка стоимости используется для прогнозирования общего ожидаемого вознаграждения от текущего состояния агента. Таким образом, этот параметр балансирует между менее предвзятой, но более высокой оценкой дисперсии (длительный временной горизонт) и более предвзятой, но менее разнообразной оценкой (короткий временной горизонт).     
    
### summary_freq: 10000
Количество опыта, которое необходимо собрать перед созданием и отображением статистики обучения. Это определяет степень детализации графиков в Tensorboard.    

### Decision Requester
Компонент DecisionRequester автоматически запрашивает решения для экземпляра агента через регулярные промежутки времени. 
DecisionPeriod - период принятия решений.
Частота, с которой агент запрашивает решение. Период принятия решения, равный 10, означает, что Агент будет запрашивать решение каждые 10 шагов.
    
### Behavior Parameters
Параметры поведения класса. Компонент для настройки поведения экземпляра агента и его свойств.
В параметрах поведения примера используется "Space Size" размером 8. Это означает, что вектор признаков, содержащий наблюдения Агента, содержит восемь элементов: компоненты x и z вращения куба агента и компоненты x, y и z относительного положения и скорости шара.
В поле Continuous Action (непрерывные действия) стоит значение 2. Это нужно для управления количеством вращений x и z, которые он должен применять к себе, чтобы шар оставался сбалансированным.
    
## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости.

- Сначала создадим предыдущую модель, в которой находится шар, плоскость и уже два куба:

![image](https://user-images.githubusercontent.com/101496751/196980583-bb3095b6-50b2-46f1-8695-24886ed89d30.png)

- Далее нужно написать такой скрипт:

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
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }
    public GameObject FirstTarget;
    public GameObject SecondTarget;
    private bool firstTargetCollected;
    private bool secondTargetCollected;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }
        FirstTarget.transform.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
        SecondTarget.transform.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
        FirstTarget.SetActive(true);
        SecondTarget.SetActive(true);
        firstTargetCollected = false;
        secondTargetCollected = false;
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(FirstTarget.transform.localPosition);
        sensor.AddObservation(SecondTarget.transform.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(firstTargetCollected);
        sensor.AddObservation(secondTargetCollected);
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
        float distanceToFirstTarget = Vector3.Distance(this.transform.localPosition, FirstTarget.transform.localPosition);
        float distanceToSecondTarget = Vector3.Distance(this.transform.localPosition, SecondTarget.transform.localPosition);
        if (!firstTargetCollected & distanceToFirstTarget < 1.42f)
        {
            firstTargetCollected = true;
            FirstTarget.SetActive(false);
        }
        if (!secondTargetCollected & distanceToSecondTarget < 1.42f)
        {
            secondTargetCollected = true;
            SecondTarget.SetActive(false);
        }
        if(firstTargetCollected & secondTargetCollected)
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

- Настройки шара такие же, как в первом задании.
- После обучения модели в несколько этапов (3 модели, 9, 27), модель работает вот так:

![Untitled (3)](https://user-images.githubusercontent.com/101496751/197012412-9be323b0-c9a8-42bc-ad39-407f0f3f1867.gif)

## Выводы

- В ходе лабораторной работы познакомилась с программными средствами для создания системы машинного обучения и ее интеграции в Unity. В ходе работы также выяснила, что при увеличении количества копий модели - она обучается быстрее. 
- Игровой баланс — это равновесие между персонажами, командами, тактиками игры и т.п. Баланс должен быть во времени игры, в её сложности, в сюжете (он должен быть разнообразным). И системы машинного обучения могут помогать поддерживать баланс игры. Например, усложнять миссии, если персонаж имеет высокие характеристики. Или подстраивать характеристики мира, чтобы персонажу было интересно развиваться. То есть машинное обучение позволяет более точно распределять игровые механики, которые подлежат балансу.

| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| PyCharm | [plugins/pycharm/README.md][PlPC] |
| Unity | [plugins/unity/README.md][PlU] |
| Anaconda Prompt| [plugins/anacondaprompt/README.md][PlAP] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
