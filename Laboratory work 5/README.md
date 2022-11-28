# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнил(а):
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
Интегрировать экономическую систему в проект Unity и обучить ML-Agent.

## Задание 1
### Измените параметры файла. yaml-агента и определить какие параметры и как влияют на обучение модели.

Ход работы:

- Нужно открыть проект в Unity, который представлен в методических указаниях, и ознакомиться с ним.

- С помощью Anaconda Prompt нужно активировать новый ML-агент и скачать новые библиотеки: mlagents 0.28.0, torch 1.7.1. Вводим такие команды:

```py
conda create -n MLAgents python=3.6
conda activate MLAgents
```

![image](https://user-images.githubusercontent.com/101496751/204228722-34969e3c-bf78-49ff-8744-6f085dc70b01.png)

```py
pip install mlagents==0.28.0
```

![image](https://user-images.githubusercontent.com/101496751/204228879-5a948438-9e82-404a-b91c-a040be2d59c3.png)

```py
pip install torch~=1.7.1 -f https://download.pytorch.org/whl/torch_stable.html
```

![image](https://user-images.githubusercontent.com/101496751/204228774-d3fbcc80-03c9-47e2-9be4-dc9fe7d1c7c6.png)


- Затем необходимо перейти в папку с проектом и начать обучение модели.

Изначально файл Move.cs выглядит вот так: 

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class Move : Agent
{
    [SerializeField] private GameObject goldMine;
    [SerializeField] private GameObject village;
    private float speedMove;
    private float timeMining;
    private float month;
    private bool checkMiningStart = false;
    private bool checkMiningFinish = false;
    private bool checkStartMonth = false;
    private bool setSensor = true;
    private float amountGold;
    private float pickaxeСost;
    private float profitPercentage;
    private float[] pricesMonth = new float[2];
    private float priceMonth;
    private float tempInf;

    // Start is called before the first frame update
    public override void OnEpisodeBegin()
    {
        // If the Agent fell, zero its momentum
        if (this.transform.localPosition != village.transform.localPosition)
        {
            this.transform.localPosition = village.transform.localPosition;
        }
        checkMiningStart = false;
        checkMiningFinish = false;
        checkStartMonth = false;
        setSensor = true;
        priceMonth = 0.0f;
        pricesMonth[0] = 0.0f;
        pricesMonth[1] = 0.0f;
        tempInf = 0.0f;
        month = 1;
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(speedMove);
        sensor.AddObservation(timeMining);
        sensor.AddObservation(amountGold);
        sensor.AddObservation(pickaxeСost);
        sensor.AddObservation(profitPercentage);
    }

    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        if (month < 3 || setSensor == true)
        {
            speedMove = Mathf.Clamp(actionBuffers.ContinuousActions[0], 1f, 10f);
            Debug.Log("SpeedMove: " + speedMove);
            timeMining = Mathf.Clamp(actionBuffers.ContinuousActions[1], 1f, 10f);
            Debug.Log("timeMining: " + timeMining);
            setSensor = false;
            if (checkStartMonth == false)
            {
                Debug.Log("Start Coroutine StartMonth");
                StartCoroutine(StartMonth());
            }

            if (transform.position != goldMine.transform.position & checkMiningFinish == false)
            {
                transform.position = Vector3.MoveTowards(transform.position, goldMine.transform.position, Time.deltaTime * speedMove);
            }

            if (transform.position == goldMine.transform.position & checkMiningStart == false)
            {
                Debug.Log("Start Coroutine StartGoldMine");
                StartCoroutine(StartGoldMine());
            }

            if (transform.position != village.transform.position & checkMiningFinish == true)
            {
                transform.position = Vector3.MoveTowards(transform.position, village.transform.position, Time.deltaTime * speedMove);
            }

            if (transform.position == village.transform.position & checkMiningStart == true)
            {
                checkMiningFinish = false;
                checkMiningStart = false;
                setSensor = true;
                amountGold = Mathf.Clamp(actionBuffers.ContinuousActions[2], 1f, 10f);
                Debug.Log("amountGold: " + amountGold);
                pickaxeСost = Mathf.Clamp(actionBuffers.ContinuousActions[3], 100f, 1000f);
                Debug.Log("pickaxeСost: " + pickaxeСost);
                profitPercentage = Mathf.Clamp(actionBuffers.ContinuousActions[4], 0.1f, 0.5f);
                Debug.Log("profitPercentage: " + profitPercentage);

                if (month != 2)
                {
                    priceMonth = pricesMonth[0] + ((pickaxeСost + pickaxeСost * profitPercentage) / amountGold);
                    pricesMonth[0] = priceMonth;
                    Debug.Log("priceMonth: " + priceMonth);
                }
                if (month == 2)
                {
                    priceMonth = pricesMonth[1] + ((pickaxeСost + pickaxeСost * profitPercentage) / amountGold);
                    pricesMonth[1] = priceMonth;
                    Debug.Log("priceMonth: " + priceMonth);
                }

            }
        }
        else
        {
            tempInf = ((pricesMonth[1] - pricesMonth[0]) / pricesMonth[0]) * 100;
            if (tempInf <= 6f)
            {
                SetReward(1.0f);
                Debug.Log("True");
                Debug.Log("tempInf: " + tempInf);
                EndEpisode();
            }
            else
            {
                SetReward(-1.0f);
                Debug.Log("False");
                Debug.Log("tempInf: " + tempInf);
                EndEpisode();
            }
        }
    }

    IEnumerator StartGoldMine()
    {
        checkMiningStart = true;
        yield return new WaitForSeconds(timeMining);
        Debug.Log("Mining Finish");
        checkMiningFinish = true;
    }

    IEnumerator StartMonth()
    {
        checkStartMonth = true;
        yield return new WaitForSeconds(60);
        checkStartMonth = false;
        month++;

    }
}
```

- Обучим модель и с помощью графиков посомтрим на результат обучения:
![image](https://user-images.githubusercontent.com/101496751/204237070-1ca94a82-f23f-4ee6-9970-1ec7de61130c.png)
![image](https://user-images.githubusercontent.com/101496751/204244449-8618f7df-5902-4059-8e0a-f1c68c46725b.png)


Установим TensorBoard для оценки результатов обучения:
![image](https://user-images.githubusercontent.com/101496751/204229940-db7a5d4d-de37-49e4-bfe6-65e90a26548c.png)
Вот такие результаты: 
![image](https://user-images.githubusercontent.com/101496751/204245238-ac1938ad-45d9-4a8d-9ac6-2931ce61ff99.png)


 - Во втором запуске мы уменьшим диапозон скорости движения вдвое: 

speedMove = Mathf.Clamp(actionBuffers.ContinuousActions[0], 1f, 5f);

![image](https://user-images.githubusercontent.com/101496751/204246960-4d64e938-d808-4e5a-9497-4f974049020d.png)
![image](https://user-images.githubusercontent.com/101496751/204247220-40a2fe34-174f-4ea4-8add-28194d91d59d.png)

При уменьшении диапозона скорости график растет вверх

- В третьем запуске мы вернем скорости начальные диапозоны, но увеличим диапозон времени добычи вдвое: 

timeMining = Mathf.Clamp(actionBuffers.ContinuousActions[1], 1f, 20f);

![image](https://user-images.githubusercontent.com/101496751/204249115-aa1302e3-679d-40e6-9562-f2cfa2f8c778.png)
![image](https://user-images.githubusercontent.com/101496751/204249443-1366c027-cd5c-4515-88c1-212b5bc286e5.png)

При увеличении времени добычи график немонотонный (он как падал, так и возрастал)

- В четвертом запуске уменьшим диапозон стоимости кирки в пять раз: 

pickaxeСost = Mathf.Clamp(actionBuffers.ContinuousActions[3], 100f, 200f);

![image](https://user-images.githubusercontent.com/101496751/204251058-d60a6b02-d115-49a5-b949-a4a740d5402c.png)
![image](https://user-images.githubusercontent.com/101496751/204251250-1c0edb65-77a7-40d0-9c8c-02e99815ecaf.png)

- В пятом запуске увеличим диапозон количества золота в 1,5 раза: 

amountGold = Mathf.Clamp(actionBuffers.ContinuousActions[2], 1f, 15f);
![image](https://user-images.githubusercontent.com/101496751/204252863-1ebc0c2a-79e7-4c2e-9deb-e40161d8238e.png)
![image](https://user-images.githubusercontent.com/101496751/204253020-f5c8db39-6389-438a-af1f-2d0a2e3c3316.png)

- второй запуск 1
strength: 2.0
![image](https://user-images.githubusercontent.com/101496751/204290208-e0dc8b3d-20f6-4e1c-b43e-ff49d4586846.png)
![image](https://user-images.githubusercontent.com/101496751/204301788-03ea388f-bd32-41f3-937d-5a7479f0ba9c.png)


-третий запуск 2
epsilon: 0.3
![image](https://user-images.githubusercontent.com/101496751/204298885-80ffacce-9331-45f4-b869-65bd1af76e16.png)
![image](https://user-images.githubusercontent.com/101496751/204301839-7cd35be5-9e50-4a30-bd78-5698c14a7d94.png)


-четвертый запуск 3
num_epoch: 7
![image](https://user-images.githubusercontent.com/101496751/204301188-290962df-2593-41a7-a0b5-d2ce7ea4ad86.png)
![image](https://user-images.githubusercontent.com/101496751/204301902-a7f603a2-2c3f-46ee-b9f7-a83ffd2dd620.png)


-пятый запуск 4
epsilon: 0.1
![image](https://user-images.githubusercontent.com/101496751/204296952-60153e9a-c250-4d92-a0d0-abb4f3a8139c.png)
![image](https://user-images.githubusercontent.com/101496751/204302058-abf4192d-9dbc-4bed-9ac7-97c1ab112ea8.png)














## Задание 2
### Опишите результаты, выведенные в TensorBoard.
    
## Выводы


| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| PyCharm | [plugins/pycharm/README.md][PlPC] |
| Unity | [plugins/unity/README.md][PlU] |
| Anaconda Prompt| [plugins/anacondaprompt/README.md][PlAP] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
