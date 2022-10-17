# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Амбрушкевич Артем Антонович
- РИ-211002

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

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python – Unity с помощью MLAgents.
Ход работы:  
  1. Создал новый пустой 3D проект на Unity.  
  2. Скачал папку с ML агентом.  
  3. В созданный проект добавил ML Agent.  
  4. Далее запустил Anaconda Prompt для возможности запуска команд через консоль, создал виртуальную среду и ввёл следующие команды для создания и активации нового ML-агента, а также для скачивания необходимых библиотек.  
    ```
    conda create -n MLAGENT python=3.6.13
    ```  
    ```
    conda activate MLAGENT
    ```  
    ```
    pip install mlagents==0.28.0
    ```  
    ```
    pip install torch~=1.7.1 -f https://download.pytorch.org/whl/torch_stable.html
    ```  
    Перешел в папку с unity-проектом
    ```
    cd /d H:\ВУЗ\3 семестр\Дата сайнс в примерах и задачах\lab-3\MLAtest-lab-3
    ```  
    
  5. Создал на сцене плоскость, куб и сферу. Создал простой C# скрипт-файл и подключил его к сфере. Скрипт:   
  ```c#
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
 6. Объекту «сфера» добавил компоненты **Rigidbody**, **Decision Requester**, **Behavior Parameters** и настроил их.  
 7. В корень проекта добавил файл конфигурации нейронной сети ```rollerball_config.yaml```.  
 8. Запустил работу ml-агента:  ```mlagents-learn rollerball_config.yaml --run-id=RollerBall --force```  
 9. Вернулся в проект Unity, запустил сцену, проверил работу ML-Agent’a.  
 10. Сделал 3 копии модели «Плоскость-Сфера-Куб», запустил симуляцию сцены и наблюдал за результатом обучения модели.
       ![image](https://user-images.githubusercontent.com/97295011/196262529-e5a05d16-2e52-4d25-9f7f-84e8fff465e7.png)
 11. Сделал 9 копии модели «Плоскость-Сфера-Куб», запустил симуляцию сцены и наблюдал за результатом обучения модели.
       ![image](https://user-images.githubusercontent.com/97295011/196262693-feba73ab-6208-43f5-b927-312c49ea2466.png)  
 13. Сделал 27 копии модели «Плоскость-Сфера-Куб», запустил симуляцию сцены и наблюдал за результатом обучения модели.
       ![image](https://user-images.githubusercontent.com/97295011/196262741-89174ef3-5a33-48bd-a278-524198910675.png)  
 14. После завершения обучения проверил работу модели.  
       ![gif](https://user-images.githubusercontent.com/97295011/196262924-4af23345-fb36-461d-b2d4-726466c5fcd4.gif)  
 15. Выводы из 1 задания: успешно подключил ML-агент, успешно обучил модель и протестировал её.




## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
