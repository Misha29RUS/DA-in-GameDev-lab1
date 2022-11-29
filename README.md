# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
- Пичугин Михаил Сергеевич 
- РИ-210932
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

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
Ознакомиться с основным устройством перцептрона, применив полученные на лекции данные на практике.

## Задание 1
### Реализовать перцептрон, который будет выполнять вычисления следующих логических операторов:

1.or

2.and

3.nand

4.xor

Первым делом создал новый проект в Unity, создал объект Empty, переименовал его в Perceptron и применил к нему скрипт:

```cs
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

![4](https://user-images.githubusercontent.com/114404329/204625715-753bfbb3-8385-46c2-bc2c-9042dcd9a5f2.PNG)

Далее поэтапно начал реализовывать логические операторы 

#### OR 

Заполнил данными 

![or 1 0](https://user-images.githubusercontent.com/114404329/204626403-b4b763e2-bad1-4071-87ac-de66e1b2a507.PNG)

Результат

![Or 1 1 (на 3 эпохе)](https://user-images.githubusercontent.com/114404329/204626465-68f750bc-ad8a-43b3-86e4-e9b26f2fe88f.PNG)

![or 1 2](https://user-images.githubusercontent.com/114404329/204626491-aae7037e-a240-4668-8a03-be3bc0009353.PNG)

Перцептрон обучился на 3 эпохе, выдав правильное значение. Работает корректно.

#### AND 

Заполнил данными 

![and 1 0](https://user-images.githubusercontent.com/114404329/204627424-a02bfcd2-b0b4-4631-a297-0387e9d81998.PNG)

Результат

![and 1 1(На 6 эпохе)](https://user-images.githubusercontent.com/114404329/204627497-7aafc60f-3158-47b0-ada6-48d196fae8f6.PNG)

![and 1 2](https://user-images.githubusercontent.com/114404329/204627529-c267a04e-bdcd-4d78-b631-353f1633cde6.PNG)

Перцептрон обучился на 6 эпохе, выдав правильное значение. Работает корректно.

#### NAND

Заполнил данными 

![NAND 3 0](https://user-images.githubusercontent.com/114404329/204628837-d766d306-7d93-4bc8-8798-05ed8bf7860a.PNG)


Результат

![NAND 3 1](https://user-images.githubusercontent.com/114404329/204628891-0794468f-4b45-4f54-98fa-f34532469c12.PNG)

![NAND 3 2(6 эпоха)](https://user-images.githubusercontent.com/114404329/204628911-d435a3ab-d0a7-4dbc-bff1-02f02c6bd8ae.PNG)


Перцептрон обучился на 6 эпохе, выдав правильное значение. Работает корректно.

#### XOR

Заполнил данными 

![XOR 4 0](https://user-images.githubusercontent.com/114404329/204628972-fb69c560-f711-4299-b7f5-51124a0b1990.PNG)

Результат

![XOR 4 1(не учится)](https://user-images.githubusercontent.com/114404329/204628991-9503ec3a-0dce-449f-abec-a0c9a044cd6d.PNG)

Перцептрон не обучился.

Попробовал увеличить колличество эпох до 100

```cs
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
		Train(100);
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}

```

![XOR 4 2(не учится)](https://user-images.githubusercontent.com/114404329/204628995-29a283c6-a585-461c-b876-a2c26658bdce.PNG)

Перцептрон не обучился, не выдав правильного значение. Работает некорректно.

## Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения.

Перенёс данные и построил график. Сделал вывод, что необходимое количество эпох имеет прямую зависимость от ошибок, которые выдает программа на тестируемых данных. Также оно зависит от сложности вычислений(задачи), случайности, которая используется для присвоения значения весов. Но не для XOR: увеличение эпох не помогло обучить процептрон для выполнения этой задачи.

![Задание 2](https://user-images.githubusercontent.com/114404329/204630149-4d5ba510-0118-455d-8a75-6aaf7ff96b76.PNG)


## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity.




## Выводы

В этой лабораторной работе я познакомился с перцептроном. Выяснил, что с помощью перцептрона можно использовать двоичную логику, но с XOR процептрон работать не может. Визуализировал закономерность эпох обучения процептрона и выяснил, что оператору OR процептрон обучается проще всего.

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
