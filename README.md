# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
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
Познакомиться с программными средствами для организации передачи данных между инструментами Google, Python и Unity.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets - Unity. При выполнении задания использовать видео-материалы и исходные данные, предоставленные преподавателями курса.

Ход работы:
- В облачном сервисе google console подключить API для работы с google sheets и google drive.

Сначала я создала новый проект в сервисе Google Console - "UnityDataSciece". Потом подключила необходимые API из маркетплейса, а именно Google Sheets API и Google Drive API. Создала сервис аккаунт UnityServiceDataScience и затем указала роль "Editor". После я выгрузила API-ключ. При создании сервиса появился Email, который необходимо занести в доступ в новую google-таблицу. Скриншот о проделанной работе:

![image](https://user-images.githubusercontent.com/101496751/194287680-254236b1-ed3f-4f4a-8c8c-30725df1e584.png)


- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

В новом проекте на PyCharm нужно открыть настройки и добавить в проект пакеты "gspread" и "numpy".
Далее нужно написать скрипт на Python:

```py
import gspread
import numpy as np
gs = gspread.service_account(filename='unitydatascience-364614-43a4b5846192.json')
sheet = gs.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        temp_inf = ((price[i - 1] - price[i - 2]) / price[i - 2]) * 100
        temp_inf = str(temp_inf)
        temp_inf = temp_inf.replace('.', ',')
        sheet.sheet1.update(('A' + str(i)), str(i))
        sheet.sheet1.update(('B' + str(i)), str(price[i - 1]))
        sheet.sheet1.update(('C' + str(i)), str(temp_inf))
        print(temp_inf)
```

Полученные данные из скрипта описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период. Как можно увидеть из скриншотов - данные в консоли и данные в google-таблице совпадают:
![image](https://user-images.githubusercontent.com/101496751/194106358-e54e7805-d7e2-49b2-bb6b-c7cfdf5feda7.png)
![image](https://user-images.githubusercontent.com/101496751/194106597-515a5a82-290a-48fa-9ac6-ea2b33907d2f.png)

- Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.

Для начала создадим API Key:
![image](https://user-images.githubusercontent.com/101496751/194109564-7cad6a64-0848-48e5-a1d5-ddcd43e0f7aa.png)
Поставим ограничивающий ключ Google Sheets API:
![image](https://user-images.githubusercontent.com/101496751/194109849-9adb6def-581e-4197-a98c-21c49936f410.png)

Создаем проект на Unity с названием UniteDataScience:
![image](https://user-images.githubusercontent.com/101496751/194110509-32a9c25f-549c-4a02-aeb5-01a9c81e98ad.png)

После нужно перенести в Assets файлы jsonPackage и soundPackage из предложенного матриала:
![image](https://user-images.githubusercontent.com/101496751/194111940-0b643c1e-3139-4d27-9b1a-e5140f9e42e7.png)
![image](https://user-images.githubusercontent.com/101496751/194112106-0d9e79d5-5f04-403d-8fb6-7ce7aa0af72f.png)

Далее создается объект и в него добавляется компонент Audio Source:
![image](https://user-images.githubusercontent.com/101496751/194112535-fcdcb204-dc1c-4b68-bd6f-b91061dfd0d0.png)

А затем добавляется новый созданный скрипт с данным кодом:

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
   
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1hOqMnl84YIYtB1-lRRwTsukZGh4APB9I-rt9tJUAM38/values/Лист1?key=AIzaSyA689pwzro5fs8-nzfvRiLECjse-uKT_rU");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
        Debug.Log(dataSet["Mon_1"]);
    }

}
```

Можем заметить, что значение из консоли совпадает с первым значением из таблицы:
![image](https://user-images.githubusercontent.com/101496751/194116305-a97ae3ec-1765-4c8f-ba92-2e4c5e2b10b8.png)
![image](https://user-images.githubusercontent.com/101496751/194116344-c5a0564e-19e4-4c45-a8ce-d06dcda8af79.png)

Значит, связка Unity и Google Sheets удалась!


- Написать функционал на Unity, в котором будет воспроизводиться аудио-файл в зависимости от значения данных из таблицы.

Дополняем наш код, чтобы выводились все значение и звуковое сопровождение к ним, теперь он выглядит так:
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1hOqMnl84YIYtB1-lRRwTsukZGh4APB9I-rt9tJUAM38/values/Лист1?key=AIzaSyA689pwzro5fs8-nzfvRiLECjse-uKT_rU");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

Переносим аудио-файлы в скрипт:
![image](https://user-images.githubusercontent.com/101496751/194118007-01d21955-efe1-4811-ba19-cc508e649ce2.png)

Теперь в консоли выводят вся данные из google-таблицы и звуковое сопровождение. Для значение больших ста: "Вы самый жестокий тиран в королестве, Ваша Светлость"; для значений в диапозоне от 10 до 100: "Люди доверяют Вам, милорд"; для значений меньше 10: "Люди восхищаются Вами, мой Лорд".
![image](https://user-images.githubusercontent.com/101496751/194119255-d1208327-97dc-45b3-92e0-c54111a3889d.png)


## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы №1.

Для начала нужно проделать всю ту же самую работу с Google Console:
![image](https://user-images.githubusercontent.com/101496751/194292003-d0b104f4-49e1-4de8-bc83-812ea25b95e9.png)
Создать лист в google-таблицах по аналогии из первого задания. Затем нужно переделать код линейной регрессии из лабораторной работы №1 также по аналогии с кодом из задания 1. В прошлой ЛР выяснилось, что чем больше паремтр Lr тем больше разница между потерями, этим мы и воспользуемся.
Будем выгружать разницу между текущей и предыдущей величиной loss.
Новый код представлен ниже:

```py
import numpy as np
import gspread

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

def model(a, b, x):
  return a*x + b

def loss_function(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  return (0.5 / num) * (np.square(prediction - y)).sum()

def optimize(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  da = (1.0 / num) * ((prediction - y) * x).sum()
  db = (1.0 / num) * ((prediction - y).sum())
  a = a - Lr * da
  b = b - Lr * db
  return a, b

def iterate(a, b, x, y, times):
  for i in range(times):
    a, b= optimize(a, b, x, y)
  return a, b

a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.0001

gc = gspread.service_account(filename='regression-364710-2384e60c3dac.json')
sh = gc.open("RegressionSheet")

prev_loss = 0

for i in range(10):
    a, b = iterate(a, b, x, y, i + 1)

    prediction = model(a, b, x)
    temp_loss = loss_function(a, b, x, y)

    diff_loss = abs(temp_loss - prev_loss)
    prev_loss = temp_loss

    sh.sheet1.update(('A' + str(i + 1)), str(i + 1))
    sh.sheet1.update(('B' + str(i + 1)), str(temp_loss))
    sh.sheet1.update(('C' + str(i + 1)), str(diff_loss))
```
Данные появились в google-таблице:
![image](https://user-images.githubusercontent.com/101496751/194300336-fa7f1a7c-2ed4-4e9c-8e9e-f19786607896.png)

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.

Сначала также нужно создать новый проект и подключить необходимые файлы. Затем нужно изменить данные воспроизведения аудио-файлов из 1 задания.
Новый код с измененными границами для произведения айдио-файлов:

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 2 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 2 & dataSet["Mon_" + i.ToString()] < 300 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 300 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1lPJwP6lLr1_dAXH6hV6QgOX_BUsz2KZUSTDXSi-Mfbw/values/Лист1?key=AIzaSyD-bRuAcWrSgZeShsllpOnLy_WX2Tkzjys");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

Вывод в консоли:
![image](https://user-images.githubusercontent.com/101496751/194300819-79018e1f-6e47-4d9f-a038-d7e6340c97ab.png)


## Выводы


| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| Google.colab | [plugins/googlecolab/README.md][PlGc] |
| Unity | [plugins/unity/README.md][PlU] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
