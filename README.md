# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
РАЗРАБОТКА СИСТЕМЫ МАШИННОГО ОБУЧЕНИЯ.
Отчет по лабораторной работе #3 выполнил(а):
- Пичугин Михаил Сергеевич 
- РИ-210932
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
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.

1. Создал новый 3D проект в Unity и добавил ML Agent.

![1](https://user-images.githubusercontent.com/114404329/198079987-90343c66-5809-4ec6-88fc-a42637c98484.png)

2. Скачал необходимые компаненты (mlagents 0.28.0 и torch 1.7.1)

![Снимок](https://user-images.githubusercontent.com/114404329/198080073-c7054e17-9ff4-4689-af41-833ad1a93ad5.PNG)

3.Создал и перекрасил куб,шар. Также добавил проскость. После объединил их в группу.

![3](https://user-images.githubusercontent.com/114404329/198080094-a65b5ae9-5b31-488a-80fa-7521cdbb9afb.PNG)

4.Добавил код в скрипт RollerAgent

```cs
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

![4](https://user-images.githubusercontent.com/114404329/198080107-f09007eb-40f5-4a02-9cfc-f42ca2514787.PNG)

5.Добавил Decision Requester, Behavior Parameters к шару

![4 1](https://user-images.githubusercontent.com/114404329/198082981-2aedec89-9c7e-4104-94af-8c71be1d98a9.PNG)

6. Добавил в проект файл с конфигурациями. В него поместил следующий код:

```yaml
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

![5](https://user-images.githubusercontent.com/114404329/198080122-0f1e0aee-4b43-4e2d-b9fa-60a372205f66.PNG)

7.Начал обучение моделе на примере одной копии модели «Плоскость-Сфера-Куб»

![6](https://user-images.githubusercontent.com/114404329/198080133-a64e106b-a79b-4ca1-a11d-c25097798b90.PNG)

![7](https://user-images.githubusercontent.com/114404329/198080326-daadbaba-0d58-415f-a91a-7cae734b356a.PNG)

8. Продолжил обучение с 9 копиями модели «Плоскость-Сфера-Куб»

![8 1](https://user-images.githubusercontent.com/114404329/198080340-08a56b25-c0a5-465a-8c6a-eff559c015ed.PNG)

9. Увеличил колличество копий модели «Плоскость-Сфера-Куб» до 36

![9](https://user-images.githubusercontent.com/114404329/198080350-2cf284b6-ed85-4590-bc5c-eb2f39e0f257.PNG)

10.Проверил работу обученной модели

![10](https://user-images.githubusercontent.com/114404329/198080365-3a6f427c-43c8-4687-9075-e3b4d3f12ee2.PNG)



https://user-images.githubusercontent.com/114404329/198095647-49889671-78ce-4167-81cb-e6e7f1c1b0d7.mp4



## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных на сфере.

```yaml
behaviors:  
   RollerBall: # название конфигурации  
    trainer_type: ppo # Задаёт вид обученияю. Задано обучение с подкреплением 
    hyperparameters: # Гиперпараметры для ppo  
      batch_size: 10 # Число опытов в каждой итерации градиентного спуска  
      buffer_size: 100 # Количество опытов, для обновления модели поведения
      # Соответствует тому, сколько опыта должно быть собрано, прежде чем мы будем изучать или обновлять модель  
      learning_rate: 3.0e-4 # Начальная скорость обучения 
      beta: 5.0e-4 # Сила энтропийной регуляризации  
      epsilon: 0.2 # Влияет на то, насколько быстро поведение может изменятся во время обучения  
      lambd: 0.99 # Параметр регуляризации, используемый при расчете обобщенной оценки преимущества  
      num_epoch: 3 # Количество проходов через буфер опыта 
      learning_rate_schedule: linear # Параметр измениения скорости обучения с течением времени  
    network_settings:  
      normalize: false # Параметр, определяющий автоматическое нормализование обучения  
      hidden_units: 128 # Число юнитов в скрытых слоях нейронной сети  
      num_layers: 2 # Число скрытых слоев в нейронной сети  
    reward_signals:  
      extrinsic:   
        gamma: 0.99 # Коэффициент поощерения,должен быть меньше единицы
        strength: 1.0 # Коэффициент силы, на который умножается поощерение 
    max_steps: 500000 # Максимальное количество повторов симуляции сцены. 
    time_horizon: 64 # Количество циклов ML агента, хранящихся в буфере до ввода в модель  
    summary_freq: 10000 # Частота сохранения статистики тренировок по шагам
```

### DecisionRequester
Компонент C#, который вызывает RequestDecision(процесс принятия решений) агента автоматически,учитывая заданный интервал.

### BehaviourParameters
Компонент для настройки поведения экземпляра агента и его свойств.

## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости.

1. Добавил в сцену ещё один куб

![3 1](https://user-images.githubusercontent.com/114404329/198103470-f77ff569-21ea-48da-9f55-6b1afe7783aa.PNG)

2.Изменил код 

```cs
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
    public Transform TargetTwo;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
        TargetTwo.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(TargetTwo.localPosition);
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
        float distanceToTargetTwo = Vector3.Distance(this.transform.localPosition, TargetTwo.localPosition);

        if(distanceToTarget < 1.42f || distanceToTargetTwo < 1.42f)
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

3. Сделал добавленный куб ещё одним таргетом

![3 2](https://user-images.githubusercontent.com/114404329/198279771-88e4b867-143f-4482-837c-68ba61b34ab8.PNG)

4.Начал обучение на одной системе «Плоскость-Сфера-Куб-Куб»

![3 3](https://user-images.githubusercontent.com/114404329/198279787-eeacd64b-805f-4d84-b558-730759498c0e.PNG)

5.Продолжил обучение на 9-ти системах «Плоскость-Сфера-Куб-Куб»

![3 4](https://user-images.githubusercontent.com/114404329/198279798-8bd2e3ce-cf37-4e51-92d0-a8af47ad1193.PNG)

6.Проверил работу обученной модели

![4 5](https://user-images.githubusercontent.com/114404329/198281546-48411e59-cebf-4743-936e-82d176067848.PNG)



https://user-images.githubusercontent.com/114404329/198279679-8f0eec6e-5e41-4c3e-afa5-38a544d8588c.mp4


## Выводы

Я считаю,что игровой баланс - это баланс между сложностью игры и удовольствием от неё. Настройка баланса - сложный процесс, однако машинное обучение упрощает эту задачу. К примеру, помогает сделать определенные участки игры сложными, но доступными для прохождения игроку.

В этой лабораторной работе я научился интегрировать нейронную сеть в проект unity.Также создавать и обучать MlAgent, который позволил шару успешно добираться до своей цели.

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
