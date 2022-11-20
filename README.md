# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
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

## Цель работы Познакомиться с работой перцептрона на практике при помощи движка Unity

## Задание 1
### В проекте реализовать перцептрон, который умеет производить вычисления: OR, AND, NAND, XOR, и дать комментарии о кореектности работы.

Ход работы: 

- Для начала создадим пустой проект на Unity. Создадим пустой объект и прикрепим ему код из методических указаний:

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class TrainingSet
{
	public double[] input;
	public double output;
}

public class Perceptron : MonoBehaviour {

	public TrainingSet[] ts;
	double[] weights = {0,0};
	double bias = 0;
	double totalError = 0;

	double DotProductBias(double[] v1, double[] v2) 
	{
		if (v1 == null || v2 == null)
			return -1;
	 
		if (v1.Length != v2.Length)
			return -1;
	 
		double d = 0;
		for (int x = 0; x < v1.Length; x++)
		{
			d += v1[x] * v2[x];
		}

		d += bias;
	 
		return d;
	}

	double CalcOutput(int i)
	{
		double dp = DotProductBias(weights,ts[i].input);
		if(dp > 0) return(1);
		return (0);
	}

	void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f);
		}
		bias = Random.Range(-1.0f,1.0f);
	}

	void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; 
		}
		bias += error;
	}

	double CalcOutput(double i1, double i2)
	{
		double[] inp = new double[] {i1, i2};
		double dp = DotProductBias(weights,inp);
		if(dp > 0) return(1);
		return (0);
	}

	void Train(int epochs)
	{
		InitialiseWeights();
		
		for(int e = 0; e < epochs; e++)
		{
			totalError = 0;
			for(int t = 0; t < ts.Length; t++)
			{
				UpdateWeights(t);
				Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
			}
			Debug.Log("TOTAL ERROR: " + totalError);
		}
	}

	void Start () {
		Train(8);
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}
```

- Реализация операции OR | ИЛИ:
Сначала вспомним табличку для этой логической операции:

 0 OR 0 = 0
 0 OR 1 = 1
 1 OR 0 = 1
 1 OR 1 = 1
 
 Установим эти значения для нашего скрипта: 
 ![image](https://user-images.githubusercontent.com/101496751/202898962-35b4e486-855f-4315-94dc-d58c16309a64.png)

Для начала зададим 1 обучающую эпоху и посмотрим на результаты наших тестов:
![image](https://user-images.githubusercontent.com/101496751/202899006-bb0db6c8-f5c4-4495-b1ca-095e6359c585.png)

Значение Total Error отвечает за обучение перцептрона: если оно отлично от нуля, то модель не обучилась, если же нуль - тогда модель успешно обучилась. При первой эпохе обучения значение Total Error = 2, и из результатов тестов видно, что значения отличны от истины: в первом случае, ответ 1, хотя должен быть 0.

Зададим 4 эпохи обучения:![image](https://user-images.githubusercontent.com/101496751/202899129-480acc4b-70a8-4204-a909-46c28eace499.png)
![image](https://user-images.githubusercontent.com/101496751/202899145-a30bdfdc-e5d0-48b9-884c-f3a86e952e4f.png)
![image](https://user-images.githubusercontent.com/101496751/202899155-1dbaa985-f025-494b-b895-4d47d0c798b0.png)

В данном случае уже при четвертой эпохе обучения Totsl Error равняется нулю, и мы видим из результатов тестов, что модель успешно обучилась.
Повторим запуск программы с 4 эпохами обучения: 
![image](https://user-images.githubusercontent.com/101496751/202899247-ca278d4a-f633-4e15-976e-c265f012986a.png)
![image](https://user-images.githubusercontent.com/101496751/202899257-ddd5467f-adb1-4de5-a756-90f0148d0547.png)
![image](https://user-images.githubusercontent.com/101496751/202899264-06e003bd-3904-4ff2-addb-7fee37ae76b9.png)

Можем сделать вывод, что не всегда 4 эпохи обучения достаточно, бывает, что и при 4 итерации Total Error отлично от нуля и программ работает некорректно. 

В ходе подбора количества эпох обучения, в моем случае на пятой эпохе значение Total Error равно нулю. (Подробные значения на каждой эпохе буду разбирать во втором задании с графиками.)



## Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения.
    
## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.

## Выводы


| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| PyCharm | [plugins/pycharm/README.md][PlPC] |
| Unity | [plugins/unity/README.md][PlU] |
| Anaconda Prompt| [plugins/anacondaprompt/README.md][PlAP] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
