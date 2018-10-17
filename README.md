
# Теория машинного обучения #
## Метрические алгоритмы классификации ##
Метрические алгоритмы классификации - алгоритмы, основанные на вычислении оценок сходства между объектами. Одним из вариантов сходства является расстояние между объектами. Тогда вводится функция расстояния ![](http://latex.codecogs.com/gif.latex?%24%5Crho%20%28x_1%2C%20x_2%29%24).
### Метод *k* ближайших соседей (*kNN*) ###
Один из самых простых метрических алгоритмов классификации. Работает следующим образом: пусть дан классифицируемый объект *z* и обучающая выборка ![](http://latex.codecogs.com/gif.latex?%24X%5El%24). Требуется определить класс объекта *z* на основе данных из обучающей выборки. Для этого:
1. Вся выборка ![](http://latex.codecogs.com/gif.latex?%24X%5El%24) сортируется по возрастанию расстояния от объекта *z* до каждого объекта выборки.
2. Проверяются классы *k* ближайших соседей объекта *z*. Класс, встречаемый наиболее часто, присваивается объекту *z*.  

Вход алгоритма - классифицируемый объект, обучающая выборка и параметр *k* - число рассматриваемых ближайших соседей.
Выход алгоритма - класс классифицируемого объекта.

При *k = 1* алгоритм превращается в *1NN*, то есть объекту *z* присваивается класс его первого ближайшего соседа, все остальные объекты выборки не учитываются.

Оптимальное значение *k* подбирается по Критерию Скользящего Контроля (*LOO*). Суть критерия: пусть дана обучающая выборка ![](http://latex.codecogs.com/gif.latex?%24X%5El%24). Требуется определить оптимальное значение параметра *k* для данной выборки. Для этого:
1. Из выборки удаляется *i*-й объект ![](http://latex.codecogs.com/gif.latex?%24x%5Ei%24).
2. Выбранный алгоритм классификации запускается для ![](http://latex.codecogs.com/gif.latex?%24x%5Ei%24) и оставшейся выборки. По окончании работы алгоритма полученный класс объекта ![](http://latex.codecogs.com/gif.latex?%24x%5Ei%24) сравнивается с его реальным классом. При их несовпадении сумма ошибки увеличивается на 1.
3. Шаги 1 и 2 повторяются для каждого объекта выборки при фиксированном *k*. По окончании работы алгоритма полученная сумма ошибки *sum* делится на размер выборки: *sum = sum / l*.  Потом значение *k* меняется, и алгоритм повторяется для нового значения. *k* с наименьшим значением суммы ошибки будет оптимальным.
#### Реализация
При реализации алгоритма, в качестве обучающей выборки использовалась выборка ирисов Фишера. В качестве признаков объектов использовались значения длины и ширины лепестка. Значение *k* подбиралось по *LOO*.

Алгоритм:

    kNN <- function(xl, k, z) {
	  orderedXL <- sortObj(xl, z)
	  n <- dim(orderedXL)[2]
	  classes <- orderedXL[1:k, n] 
	  counts <- table(classes) # Таблица встречаемости каждого класса среди k ближайших соседей объекта
	  class <- names(which.max(counts)) # Наиболее часто встречаемый класс
	  return (class)
	}
где *xl* - обучающая выборка.

![kNN.png](https://github.com/IHappyPlant/RProjects/blob/master/img/kNN_plot.png)

Достоинства алгоритма:
1. Простота реализации
2. Хорошее качество, при правильно подобранной метрике и параметре *k*

Недостатки алгоритма:
1. Необходимость хранить выборку целиком
2. Малый набор параметров
3. Качество классификации сильно зависит от выбранной метрики

### Метод *k* взвешенных ближайших соседей (*kwNN*)
Отличается от *kNN* тем, что вес ближайших соседей зависит не от ранга соседа, а от расстояния до объекта *z*. В *kNN* каждый из *k* ближайших соседей имеет вес равный единице, а все остальные объекты выборки имеют вес, равный нулю. Поэтому можно было говорить о *частоте* появления класса среди *k* ближайших соседей. В методе *kwNN* для задания весов *k* ближайшим соседям должна использоваться невозрастающая функция. Мы будем использовать функцию `(k - i + 1) * (q^2)`, где *k* - число рассматриваемых ближайших соседей, *i* - ранг соседа, *q* - подбираемый параметр. Значение *q* подбирается для каждого *k* по *LOO*.
#### Реализация
При реализации использовалась та же выборка ирисов Фишера. Значения *k* и *q* подбирались по *LOO*  
Алгоритм:

    kwNN <- function(xl, z, k, q) {
	  orderedXL <- sortObj(xl, z);
	  n <- dim(orderedXL)[2]
	  classes <- orderedXL[1:k, n]  # Берём k ближайших соседей
	  classes <- table(classes) # Делаем для них таблицу
	  classes[1:length(classes)] <- 0 # Обнуляем все значения в таблице
	  for (i in names(classes)) { # Для каждого класса
	    for (j in 1:k) { # Проходим по всем k соседям
	      if (orderedXL[j, n] == i) # И суммируем веса всех объектов одинаковых классов
	        classes[i] = classes[i] + (k - j + 1) * (q * q)
	    }
	  }
	  class <- names(which.max(classes)) # Вернём самый большой вес
	  return (class)
	}
где *orderedXL* - отсортированная обучающая выборка.

![kwNN.png](https://github.com/IHappyPlant/RProjects/blob/master/img/kwNN_plot.png) 


Достоинства алгоритма
1. Простота реализации
2. Хорошая эффективность при правильно подобранной метрике, *k* и *q*. В общем случае, более высокая эффективность, чем у *kNN*.

Недостатки. Вообще говоря, те же, что и у *kNN*:
1. Необходимость хранить выборку целиком
2. Малый набор параметров
3. Сильная зависимость качества классификации от выбранной метрики (особенность всех метрических алгоритмов)