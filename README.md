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
- Создаю 10 сцен для дальнейшей работы с ними ![image](https://github.com/user-attachments/assets/8f105edf-cfb3-44c5-98ea-280fd3fcba15)
Далее:
Я создаю пустой объект на сцене и добавляю к нему компонент-загрузчик, который:

    - Берет настройки баланса из гугл-таблицы
    - Хранит параметры уровня (скорость, время падения яиц и т.д.)
    - Выбирает нужные значения по указанному в инспекторе номеру уровня

После загрузки данных я вызываю событие OnDataLoaded. Это нужно, чтобы:
    - Дать время на загрузку из интернета
    - Убедиться, что данные точно готовы
    - Безопасно обновить игровые параметры

Другие системы подписываются на это событие и применяют настройки, когда всё загружено. Так я избегаю проблем с асинхронной загрузкой.

```C#
using System;
using System.Collections;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;
using System.Globalization;

[System.Serializable]
public class GameSettings
{
    public float dragonSpeed;
    public float eggDropTime;
    public float mass;
    public int shieldCount;
}

public class SettingsLoader : MonoBehaviour
{
    private const string SPREADSHEET_ID = "1HUsx9fzS2M9skJoMe7ZKUz6IdCQrU7kIMfhz5zumE4M";
    private const string API_KEY = "AIzaSyAY025NahCQGwDFeFa7uJYgIBuRut70nVs";
    private const string SHEET_NAME = "Лист1";

    [SerializeField] private int _targetLevel;
    private GameSettings _currentSettings = new GameSettings();

    public GameSettings CurrentSettings => _currentSettings;
    public event Action OnSettingsLoaded;

    private void Start()
    {
        StartCoroutine(LoadSettingsFromGoogleSheets());
    }

    private IEnumerator LoadSettingsFromGoogleSheets()
    {
        string url = $"https://sheets.googleapis.com/v4/spreadsheets/{SPREADSHEET_ID}/values/{SHEET_NAME}?key={API_KEY}";
        
        using (UnityWebRequest request = UnityWebRequest.Get(url))
        {
            yield return request.SendWebRequest();

            if (request.result != UnityWebRequest.Result.Success)
            {
                Debug.LogError($"Failed to load settings: {request.error}");
                yield break;
            }

            ProcessResponse(request.downloadHandler.text);
        }

        OnSettingsLoaded?.Invoke();
    }

    private void ProcessResponse(string jsonResponse)
    {
        var parsedData = JSON.Parse(jsonResponse);
        var valuesArray = parsedData["values"].AsArray;

        for (int i = 1; i < valuesArray.Count; i++) // Skip header row
        {
            var row = valuesArray[i].AsArray;
            
            if (row.Count < 4) continue;

            if (int.TryParse(row[0], out int level) && level == _targetLevel)
            {
                _currentSettings.dragonSpeed = ParseFloat(row[1]);
                _currentSettings.eggDropTime = ParseFloat(row[2]);
                _currentSettings.shieldCount = ParseInt(row[3]);
                
                Debug.Log($"Level {level} settings loaded: " +
                         $"Speed={_currentSettings.dragonSpeed}, " +
                         $"EggDrop={_currentSettings.eggDropTime}, " +
                         $"Shields={_currentSettings.shieldCount}");
                break;
            }
        }
    }

    private float ParseFloat(string value)
    {
        return float.Parse(value.Replace(',', '.'), CultureInfo.InvariantCulture);
    }

    private int ParseInt(string value)
    {
        return int.Parse(value);
    }
}
```
Далее находим скрипты дракона и сферы для дальнейших действий:
![image](https://github.com/user-attachments/assets/c4ccc444-ef79-43d0-9262-af01efa22a97)

Создаем обработчик события для обновления нужных параметров (дракон):
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
Создаем обработчик события для обновления нужных параметров (сфера):
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

Теперь даем каждому объекту ссылку на необходимый скрипт: 

- ![image](https://github.com/user-attachments/assets/6b6c2363-7b30-46ba-ab78-adfa504b2fae)

Делаем так с каждой и получаем: 
![image](https://github.com/user-attachments/assets/f3852a2d-38f2-4784-9f09-866dd07d615a)

## Выводы
Я освоил работу с балансом игры: научился настраивать параметры, визуализировать изменения, рассчитывать сложность и внедрять эти настройки в Unity через интеграцию с Python и Google Таблицами

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
