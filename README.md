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

Первым делом нужно создать новый проект в сервисе Google Console, пусть он будет называться "UnityDataSciece":
![Снимок экрана 2022-10-05 195203](https://user-images.githubusercontent.com/101496751/194093456-0708b7b4-ea73-46da-8a1d-55047cfc5c02.png)

Дальше необходимо включить необходимые API из маркетплейса, а именно Google Sheets API и Google Drive API:
![Снимок экрана 2022-10-05 195722](https://user-images.githubusercontent.com/101496751/194093927-14f54d03-6879-4381-9956-73b1a9638f81.png)
![Снимок экрана 2022-10-05 195546](https://user-images.githubusercontent.com/101496751/194093954-0352040c-c377-446e-9739-106af76be181.png)

- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

В ходе работы необходимо создать сервис аккаунт UnityServiceDataScience, затем указать роль "Editor":
![image](https://user-images.githubusercontent.com/101496751/194094671-dead92b2-b055-49f0-81eb-372dd0441327.png)

Затем необходимо добавить ключ сервису, указав путь до папки с нашим проектом:
![image](https://user-images.githubusercontent.com/101496751/194095978-0f296a31-bd72-4b91-b196-9d46566fb836.png)

При создании сервиса у нас появился Email, который необходимо занести в доступ в новую google-таблицу:
![image](https://user-images.githubusercontent.com/101496751/194096915-6a49a4f6-460e-458a-8e15-78b103a2ca22.png)

В новом проекте на PyCharm нужно открыть настройки и добавить в проект пакеты "gspread" и "numpy":
![image](https://user-images.githubusercontent.com/101496751/194098591-7ee7c740-f98b-4742-808c-86113971a19e.png)
![image](https://user-images.githubusercontent.com/101496751/194099196-525d36ea-2c45-4b03-9022-7948779acdee.png)

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



- Написать функционал на Unity, в котором будет воспроизводиться аудио-файл в зависимости от значения данных из таблицы.








## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы №1.




## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.

## Выводы


| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| Google.colab | [plugins/googlecolab/README.md][PlGc] |
| Unity | [plugins/unity/README.md][PlU] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
