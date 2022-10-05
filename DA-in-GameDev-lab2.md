##Сбор, обработка и визуализация тестового набора данных
Отчет по лабораторной работе #2 выполнил(а):
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
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.
Ход работы:

1) Подключил необходимые API сервисы в Google Cloud( google sheets и google drive), создал свой API ключ и выгрузил ключ сервисного аккаунта(предварительно создав его)

![Alt text](https://github.com/Maksimyska/screen/blob/main/image_2022-10-05_18-22-17.png)

2) При помощи Python я реализовал запись данных из скрипта в Google Sheets. Эти данные описывают изменение темпа инфляции на протяжении 11 периодов, причем, с учетом стоимости игрового объекта в каждый из этих периодов

![Alt text](https://github.com/Maksimyska/screen/blob/main/image_2022-10-05_20-42-00.png)

3) В Юнити, создав предварительно новый проект, в котором будет получение данных из таблицы(Google Sheets), куда я записывал данные при помощи Python(предыдущий пункт). Далее был написан скрипт, который воспроизводит аудиозвук исходя из условия в скрипте и данных из таблицы(Надеюсь, вы поверите мне наслово, что звук и правда воспроизводился))))

![Alt text](https://github.com/Maksimyska/screen/blob/main/image_2022-10-05_20-42-38.png)

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1

Исходный код:
```py
import gspread
import numpy as np

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

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
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db

    return a, b

def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b

a = np.random.rand(1)
b = np.random.rand(1)

Lr = 0.000001

gc = gspread.service_account(filename='unitydatasciencekhamoyan-51bea556ee29.json')
sh = gc.open("Regress")

old_loss = 0

for i in range(10):
    a, b = iterate(a, b, x, y, 100 * (i + 1))

    prediction = model(a, b, x)
    loss = loss_function(a, b, x, y)

    diff_loss = abs(loss - old_loss)
    old_loss = loss

    sh.sheet1.update(('A' + str(i + 1)), str(i + 1))
    sh.sheet1.update(('B' + str(i + 1)), str(loss))
    sh.sheet1.update(('C' + str(i + 1)), str(diff_loss))
```

Тут действия аналогичные предыдщуему задания, но тут повторение происходит 10 раз с шагом в 100(100 итераций, 200, 300, ..., 1000) В таблицу загружается разница каждого повтоерния(модуль разницы loss и предыдущей loss)

![Alt text](https://github.com/Maksimyska/screen/blob/main/image_2022-10-05_20-47-45.png)

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2

После небольших махинаций с кодом(изменение условия выполнения запуска аудио) происходит следущее

![Alt text](https://github.com/Maksimyska/screen/blob/main/image_2022-10-05_20-52-31.png)

Ну и как же без кода?
```C#
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
        if (i > dataSet.Count) {
            return;
        }

        if (dataSet["iter_" + i.ToString()] > 1000 & statusStart == false & i <= dataSet.Count){
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["iter_" + i.ToString()] + " " + i.ToString());
        }

        if (dataSet["iter_" + i.ToString()] < 1000 & dataSet["iter_" + i.ToString()] > 100 & statusStart == false & i <= dataSet.Count){
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["iter_" + i.ToString()] + " " + i.ToString());
        }

        if (dataSet["iter_" + i.ToString()] < 100 & statusStart == false & i <= dataSet.Count){
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["iter_" + i.ToString()] + " " + i.ToString());
        }
    }

    IEnumerator GoogleSheets()
    {   
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1RSPPF-ez4t3ongewW7rC87CyTVH-7CghSiWId_14ZpE/values/Лист1?key=AIzaSyALnyqcFxEbNzN3MUCdXNWZLDEHfJrR_Lc");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("iter_" + selectRow[0]), float.Parse(selectRow[2]));
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
        ++i;
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

## Выводы

Получил дейтсвительно хороший навык - запись данных в Google Sheets при помощи библиотеки gspread, которые после обрабатывал при помощи Unity и анализировал

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
