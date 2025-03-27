# in -GameDev- 3
# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Лыжин Лев Дмитриевич
- РИ-230913

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | ? |
| Задание 2 | * | ? |
| Задание 3 | * | ? |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)


## Цель работы
Разработать оптимальный баланс изменения сложности для десяти уровней игры Dragon Picker.

## Часть 1. Начало работы с прототипом
### Ознакомьтесь с презентацией третьей лекции, оцените структуры игры Dragon Picker. Скачайте и ознакомьтесь с прототипом игры Dragon Picker на движке Unity. Запустите игровую сцену, проанализируйте движение дракона:
### - Какие переменные влияют на движение дракона на сцене? Укажите эти переменные.
### - Какие переменные влияют на сложность игры на сцене? Укажите эти переменные.
Ход работы:
Перемещение дракона по сцене контролируется двумя основными параметрами:
- Скорость передвижения – определяет, насколько быстро дракон двигается.
- Случайный фактор – решает, изменит ли дракон направление в тот или иной момент.

Уровень сложности игры зависит от следующих настроек:
- скорость дракона;
- как часто появляются яйца;
- скорость их падения;
- количество доступных сфер сбора;
- как часто дракон меняет направление;
- размер сферы сбора.

## Часть 2. Начало работы с шаблоном google-таблицы для балансировки игры
### Ознакомьтесь с презентацией третьей лекции, оцените структуры шаблона таблицы для балансировки. Отметьте, как может быть использован шаблон таблицы для визуализации изменения уровней сложности в игре Dragon Picker?
Ход работы:
- шаблон таблицы позволяет фиксировать изменения показателя «баланс» на различных уровнях игры и визуализировать данные с помощью графиков для более удобного анализа

## Часть 3. Задания к работе
## - Задание 1. Предложите вариант изменения найденных переменных для 10 уровней в игре. Визуализируйте изменение уровня сложности в таблице.
Ход работы:
- Я взял 4 переменные (скорость дракона, скорость яйца и размер сферы).
- Код для заполнения таблицы данными:
```Python
import gspread
from oauth2client.service_account import ServiceAccountCredentials
import math

scope = ["https://spreadsheets.google.com/feeds", "https://www.googleapis.com/auth/drive"]
creds = ServiceAccountCredentials.from_json_keyfile_name("manifest-ocean-454918-u5-c09ff04589a3.json", scope)
client = gspread.authorize(creds)

spreadsheet = client.open("DragonPicker")
sheet = spreadsheet.sheet1

# Стартовые параметры
base_speed = 1
level = 0
base_egg_time = 3  # начальное время падения яйца (секунды)
min_egg_time = 1   # минимальное время падения
sphere_size_start = 10

# Очистка таблицы и запись заголовков
sheet.clear()
sheet.update("A1:F1", [["Level", "Speed", "Egg_time", "Sphere_size"]])

# Игровой процесс на 10 уровней
for level in range(1, 11):
    
    speed = base_speed * (1.3 ** level)
    
    egg_time = max(min_egg_time, base_egg_time - (level * 0.2))
    
    sphere_size = max(2, sphere_size_start - level)
    
    sheet.append_row([level, round(speed, 1), round(egg_time, 1), sphere_size])
    print(f"Уровень {level}, Скорость дракона: {round(speed, 1)}, Время яйца: {round(egg_time, 1)}с, Размер сферы: {sphere_size}")
```
- Таблица описывающая изменения переменных
![image](https://github.com/user-attachments/assets/5dbd5a4f-f196-419b-9ec5-409ca5fa445a)
Ссылка на таблицу: https://docs.google.com/spreadsheets/d/1NHBxtjuTTTXMR8O3U0tyKovK4e6ER2Rp_c9uyrjim-U/edit?gid=0#gid=0

- Экспоненциальный рост параметров (скорость дракона и частота яиц) обеспечивает:
  - Плавное усложнение на ранних уровнях (1.3^level даёт +30% сложности за уровень)
  - Резкий скачок сложности к финальным уровням (уровень 10 в 13.8 раз сложнее стартового)
  - Естественное ограничение максимальной сложности (формула не позволит параметрам уйти в бесконечность)
 
- Ступенчатое уменьшение щитов выбрано для:
  - Создания контрольных точек сложности (игрок чувствует "переходы" между уровнями)
  - Упрощения визуализации прогресса (целые числа лучше воспринимаются)
  - Балансировки без перекосов (предсказуемое уменьшение защиты)

- Время падения яиц уменьшается линейно с clamp-ограничением:
  - Стартовые 3 секунды дают новичкам время на адаптацию
  - Минимум 1 секунда сохраняет челлендж без невозможных уровней
  - Линейность (в отличие от экспоненты) предотвращает резкие скачки времени
  - 
## - Задание 2. Создайте 10 сцен на Unity с изменяющимся уровнем сложности.
- Для того чтобы передать параметры в заранее сделанные игровые сцены (я просто скопировал 1 сцену Dragon Picker 10 раз ![image](https://github.com/user-attachments/assets/e7618053-4e90-46c0-91a2-88f6b55f9cad)
) я сделал следующие шаги:
1. Создать пустой объект на сцене и прикрепить к нему компонент отвечающий за загрузку данных из таблицы, хранение информации об уровне, выбор и хранение параметров "баланса". Выборка параметров баланса зависит от указанном в сериализованном поле значении уровня.
Так же этот скрипт, после того как считал и определил нужные нам параметры, вызывает событие которое означает что данныве нужно поменять. Это делается для того, чтобюы выдержать паузу перед тем как прочитаются данные из гугл таблицы.

Скрипт компонента:
```C#  
using System;
using System.Collections;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;
using System.Globalization;

public class LoadSettings : MonoBehaviour
{
    [SerializeField] private int _sceneNumber;
    #region Private Fields
    private float _dragonSpeed;
    private float _timeEggDrop;
    private float _mass;
    private int _countShield;
    #endregion

    #region Getters
    public float DragonSpeed { get { return _dragonSpeed; } }
    public float TimeEggDrop { get { return _timeEggDrop; } }
    public float Mass { get { return _mass; } }
    public int CountShield { get { return _countShield; } }
    #endregion

    public event Action CoroutineIsStopped;

    void Awake()
    {
        StartCoroutine(GoogleSheets());
    }
    private float ConvertStrToFloat(string str)
    {
        //Debug.Log(str);
        str = str.Replace(',', '.');
        float result = float.Parse(str, CultureInfo.InvariantCulture.NumberFormat); ;
        return result;
    }
    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1HUsx9fzS2M9skJoMe7ZKUz6IdCQrU7kIMfhz5zumE4M/values/Лист1?key=AIzaSyAY025NahCQGwDFeFa7uJYgIBuRut70nVs");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString())[0];
            //Debug.Log(parseJson);
            string lvl = parseJson[0];
            //Debug.Log(lvl);
            var tryStr = int.TryParse(lvl, out var number);
            if (tryStr == true)
            {
                int intLvl = int.Parse(lvl);
                if (_sceneNumber == intLvl)
                {
                    Debug.Log(intLvl);
                    _dragonSpeed = ConvertStrToFloat(parseJson[1]);
                    _timeEggDrop = ConvertStrToFloat(parseJson[2]);
                    string strCount = parseJson[3];
                    _countShield = Convert.ToInt32(strCount);
                }
            }
        }
        CoroutineIsStopped?.Invoke();

    }
}
```
Объект отвечающий за загрузку параметров LoadSettings, их хранение и их передачу:
![image](https://github.com/user-attachments/assets/f67a48a2-6ef3-4327-a30c-f229506ba02a)


2. Далее нужно залезть в код в котором определяется и реализуется поведение дракона, а так же другой скрипт который делает тоже самое но с щитом
3. Нужные нам скрипты в файловой системе юнити:
![image](https://github.com/user-attachments/assets/71e8fdcc-0c71-41cf-a1d5-93878aeefad6)


В скрипте отвечающим за дракона мы создаем обработчик события, который будет обновлять нужные нам параметры:
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class DragonPicker : MonoBehaviour
{
    [SerializeField] private LoadSettings _settings;
    public GameObject energyShieldPrefab;
    public int numEnergyShield;
    public float energyShieldBottomY = -6f;
    public float energyShieldRadius = 1.5f;
    
    public List<GameObject> shieldList;

    private void OnEnable()
    {
        _settings.CoroutineIsStopped += UpdateShield;
    }

    void CreateShield()
    {
        shieldList = new List<GameObject>();

        for (int i = 1; i <= numEnergyShield; i++)
        {
            GameObject tShieldGo = Instantiate<GameObject>(energyShieldPrefab);
            tShieldGo.transform.position = new Vector3(0, energyShieldBottomY, 0);
            tShieldGo.transform.localScale = new Vector3(1 * i, 1 * i, 1 * i);
            shieldList.Add(tShieldGo);
        }
    }

    private void UpdateShield()
    {
        numEnergyShield = _settings.CountShield;
        CreateShield();
    }

    public void DragonEggDestroyed(){
        GameObject[] tDragonEggArray = GameObject.FindGameObjectsWithTag("Dragon Egg");
        foreach (GameObject tGO in tDragonEggArray){
            Destroy(tGO);
        }
        int shieldIndex = shieldList.Count - 1;
        GameObject tShieldGo = shieldList[shieldIndex];
        shieldList.RemoveAt(shieldIndex);
        Destroy(tShieldGo);

        if (shieldList.Count == 0){
            SceneManager.LoadScene("_0Scene");
        }
    }
}
```

И делаем тоже самое в скрипте который создает щит:
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyDragon : MonoBehaviour
{
    public GameObject dragonEggPrefab;
    [SerializeField] private LoadSettings _settings;
    public float speed;
    public float timeBetweenEggDrops;
    public float leftRightDistance = 10f;
    public float chanceDirection = 0.1f;

    private void OnEnable()
    {
        _settings.CoroutineIsStopped += UpdateFields;
    }


    void Start()
    {
        Invoke("DropEgg", 2f);
    }

    private void UpdateFields()
    {
        speed = _settings.DragonSpeed;
        //Debug.Log(speed);
        timeBetweenEggDrops = _settings.TimeEggDrop;
        //Debug.Log(timeBetweenEggDrops);
    }

    void DropEgg(){
        Vector3 myVector = new Vector3(0.0f, 5.0f, 0.0f);
        GameObject egg = Instantiate<GameObject>(dragonEggPrefab);
        egg.transform.position = transform.position + myVector;
        Invoke("DropEgg", timeBetweenEggDrops);
    }

    // Update is called once per frame
    void Update()
    {
        Vector3 pos = transform.position;
        pos.x += speed * Time.deltaTime;
        transform.position = pos;

        if (pos.x < -leftRightDistance){
            speed = Mathf.Abs(speed);
        }
        else if (pos.x > leftRightDistance){
            speed = -Mathf.Abs(speed);
        }
    }

    private void FixedUpdate() {
        if (Random.value < chanceDirection){
            speed *= -1;
        }
    }
}
```

Далее нужно передать каждому объекту, который реализует функционал дракона и щита, ссылку на скрипт который определяет параметры: 
![image](https://github.com/user-attachments/assets/6b6c2363-7b30-46ba-ab78-adfa504b2fae)


И так нужно сделать с каждой сценой

Итог (для примера был взят последний 10 уровень, в котором отображены измененный параметры в инспекторе):
![image](https://github.com/user-attachments/assets/4f02343c-24e7-4222-bd99-e9fb2b16b715)




Для визуализации изменений параметров был обновлен код заполнения таблицы:

![image](https://github.com/user-attachments/assets/02caa6df-fc07-47d1-b192-c4a130b828ef)


## Выводы
Я научился работать с параметрами баланса, визуализировать их, расчитывать, а также применять алгоритм изменения параметров в юнити, с помощью Python и гугл таблиц

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
