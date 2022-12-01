# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
# 4. Лабораторная работа 4 - "Перцептрон".

Отчет по лабораторной работе #4 выполнил(a):
- Ширинкина Мария Александровна
- НМТ-213929
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

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.


## Постановка задачи.
Использование перцептрона в Unity и Game Development'е, изучение его характерных свойтсв и особенностей.

## Задание 1. 
###  В проекте Unity реализовать перцептрон, который умеет производить вычисления OR | AND | NAND | XOR и дать комментарии о корректности роботы.
Код перцептрона
```C#
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
				//Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
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

Скрипт спроектирован таким образом, что для нас нет необходимости делать какие либо копии для изменения логики при обучении. Перцептрон переобучается с нуля при запуске сцены для каждого GameObject, имеющего параметр Perceptron.cs. Следовательно, остается создать Empty-объекты к которым мы прикрепляем скрипт, а после скорректируем вводные значение на вход и соответствующий им результат.

Получилось следующее:

![image](https://user-images.githubusercontent.com/114253132/205102864-42845cdb-3401-408d-8ed2-d9a3c19d2e03.png)


Настройки одного из элементов, характеризующие присущую логическую операцию:

![image](https://user-images.githubusercontent.com/114253132/205103094-e62f3f96-54e0-47e8-851d-b3f4c2baf03a.png)

Запустим сцену: 

!![image](https://user-images.githubusercontent.com/114253132/205103203-fa9f260e-a543-414d-9b76-3a421d32f30e.png)

Для прочих элементов все работает точно также.

Комментарии о корректности работы Train(16 -> 8):

- AND функционирует корекктно после 4-6 скормленных тренировок, но иногда хватает двух.
- NAND функционирует корекктно после 4-8 скармленных тренировок.
- OR функционирует корекктно после 4-6 скармленных тренировок.
- XOR не функционирует, с увеличение Train результаты становятся только хуже - TOTAL ERROR увеличивается с каждой эпохой перцептрона.

Причины полученных результатов коррелируют с возможностями прецептрона. Первые логические операторы линейны, а у последнего такой зависимости нет. Поскольку перцептрон геометрически представляет из себя прямую с различными коэф. `aX+b` , то он не может обработать закономерности, представленные на картинке слева: 

![image](https://user-images.githubusercontent.com/114253132/205104890-7f189da9-9644-4b79-a9d0-2a62e3f14db9.png)
## Задание 2. 
###  Графики зависимости ошибок TE от количества эпох.

![GRAFS for lab4](https://user-images.githubusercontent.com/114253132/205107964-5c595c32-5b34-4319-8e86-c119b71987ee.jpg)

Количество эпох зависит от изначального коэфицента веса и скорости изменения этого коэфицента. Второй фактор хаотичен и при высоких значениях перепрыгивает нужную правильную середину, в результате чего количество эпох только увеличится, но появятся случаи, когда обучение происходить быстрее. Также кол-во эпох напрямую зависит от сложности логических операторов и вида зависимости, которую мы получим. 

Влияние на веса в рамках нашей задачи.

```

void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; // реакция на ошибку
		}
		bias += error; // отклонение
	}

```
```
void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f); //первое значение случайное
		}
		bias = Random.Range(-1.0f,1.0f);
	}
```


## Выводы
В рамках лабораторной работы 4 был применен перцептор для реализации стандартных логических операторов. Также было выяснено, что корректность исполненения будет присутствовать для функций линейного типа `aX+b`
