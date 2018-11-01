



# Теория машинного обучения

## Навигация
* [Метрические алгоритмы классификации](#метрические-алгоритмы-классификации) 
	* [Сводная таблица](#сводная-таблица-по-метрическим-алгоритмам)
	* [Метод *k* ближайших соседей *(kNN)*](#метод-k-ближайших-соседей-knn)  
	* [Метод  *k*  взвешенных ближайших соседей *(kwNN)*](#метод-k-взвешенных-ближайших-соседей-kwnn)  
	* [Метод парзеновского окна](#метод-парзеновского-окна)  
	* [Метод потенциальных функций](#метод-потенциальных-функций)
	
## Метрические алгоритмы классификации
Метрические алгоритмы классификации - алгоритмы, основанные на вычислении оценок сходства между объектами. Одним из вариантов сходства является расстояние между объектами. Тогда вводится функция расстояния ![](http://latex.codecogs.com/gif.latex?%24%5Crho%20%28x_1%2C%20x_2%29%24).

### Сводная таблица по метрическим алгоритмам
| Алгоритм              | Параметры                                            | LOO   | Кол-во ошибок |
|-----------------------|------------------------------------------------------|-------|---------------|
| [kNN](#метод-k-ближайших-соседей-knn)                   | k = 6                                                | 0.03 | 5          |
| [kwNN](#метод-k-взвешенных-ближайших-соседей-kwnn)                  | k = 6, q = 1                                       | 0.03 | 5          |
| [Потенциальные функции](#метод-потенциальных-функций) | h(setosa) = 1, h(!setosa) = 0.4, Gamma_i - случайные | -     | 5             |
| [Парзеновское окно](#метод-парзеновского-окна)     | Ядро - Прямоугольное, h = 0.4                        | 0.04  | 6             |
| [Парзеновское окно](#метод-парзеновского-окна)    | Ядро - Треугольное, h = 0.4                          | 0.04  | 6             |
| [Парзеновское окно](#метод-парзеновского-окна)     | Ядро - Епанечникова, h = 0.4                         | 0.04  | 6             |
| [Парзеновское окно](#метод-парзеновского-окна)     | Ядро - Квартическое, h = 0.4                         | 0.04  | 6             |
| [Парзеновское окно](#метод-парзеновского-окна)     | Ядро - Гауссовское, h = 0.1                          | 0.04  | 6             |

### Метод *k* ближайших соседей (*kNN*)
Один из самых простых метрических алгоритмов классификации. Работает следующим образом: пусть дан классифицируемый объект *z* и обучающая выборка ![](http://latex.codecogs.com/gif.latex?%24X%5El%24). Требуется определить класс объекта *z* на основе данных из обучающей выборки. Для этого:
1. Вся выборка ![](http://latex.codecogs.com/gif.latex?%24X%5El%24) сортируется по возрастанию расстояния от объекта *z* до каждого объекта выборки.
2. Проверяются классы *k* ближайших соседей объекта *z*. Класс, встречаемый наиболее часто, присваивается объекту *z*.  

Вход алгоритма - классифицируемый объект, обучающая выборка и параметр *k* - число рассматриваемых ближайших соседей.
Выход алгоритма - класс классифицируемого объекта.

При *k = 1* алгоритм превращается в *1NN*, то есть объекту *z* присваивается класс его первого ближайшего соседа, все остальные объекты выборки не учитываются.

Оптимальное значение *k* подбирается по Критерию Скользящего Контроля (*LOO*). Суть критерия: пусть дана обучающая выборка ![xl](http://latex.codecogs.com/gif.latex?%24X%5El%24). Требуется определить оптимальное значение параметра *k* для данной выборки. Для этого:
1. Из выборки удаляется *i*-й объект ![](http://latex.codecogs.com/gif.latex?x_%7Bi%7D).
2. Выбранный алгоритм классификации запускается для ![x_i](http://latex.codecogs.com/gif.latex?x_%7Bi%7D) и оставшейся выборки. По окончании работы алгоритма полученный класс объекта ![](http://latex.codecogs.com/gif.latex?x_%7Bi%7D) сравнивается с его реальным классом. При их несовпадении сумма ошибки увеличивается на 1.
3. Шаги 1 и 2 повторяются для каждого объекта выборки при фиксированном *k*. По окончании работы алгоритма полученная сумма ошибки *sum* делится на размер выборки *l*: ![sum=sum/l](http://latex.codecogs.com/gif.latex?sum%3D%20%5Cfrac%7Bsum%7D%7Bl%7D) .  Потом значение *k* меняется, и алгоритм повторяется для нового значения. *k* с наименьшим значением суммы ошибки будет оптимальным.
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
Отличается от *kNN* тем, что вес ближайших соседей зависит не от ранга соседа, а от расстояния до объекта *z*. В *kNN* каждый из *k* ближайших соседей имеет вес равный единице, а все остальные объекты выборки имеют вес, равный нулю. Поэтому можно было говорить о *частоте* появления класса среди *k* ближайших соседей.  
В методе *kwNN* для задания весов *k* ближайшим соседям должна использоваться невозрастающая функция.  
Мы будем использовать функцию весов ![w(i)=q^i](http://latex.codecogs.com/gif.latex?w%28i%29%3Dq%5Ei) , *i* - ранг соседа, *q* - подбираемый параметр. Значение *q* подбирается для каждого *k* по *LOO*.
#### Реализация
При реализации использовалась та же выборка ирисов Фишера. Значения *k* и *q* подбирались по *LOO*  
Алгоритм:

    kwNN <- function(xl, z, k, q) {
	  orderedXL <- sortObj(xl, z);
	  n <- ncol(orderedXl)
	  classes <- orderedXL[1:k, n]     # Берём k ближайших соседей
	  classes <- table(classes)        # Делаем для них таблицу
	  classes[1:length(classes)] <- 0  # Обнуляем все значения в таблице
	  for (i in names(classes)) {      # Для каждого класса
	    for (j in 1:k) {               # Проходим по всем k соседям
	      if (orderedXL[j, n] == i)    # И суммируем веса всех объектов одинаковых классов
	        classes[i] = classes[i] + q^j
	    }
	  }
	  class <- names(which.max(classes)) # Вернём самый большой вес
	  return (class)
	}
где *xl* - обучающая выборка.

![kwNN.png](https://github.com/IHappyPlant/RProjects/blob/master/img/kwNN_plot.png) 


Достоинства алгоритма
1. Простота реализации
2. Хорошая эффективность при правильно подобранной метрике, *k* и *q*. В общем случае, более высокая эффективность, чем у *kNN*.

Недостатки. Вообще говоря, те же, что и у *kNN*:
1. Необходимость хранить выборку целиком
2. Малый набор параметров
3. Сильная зависимость качества классификации от выбранной метрики (особенность всех метрических алгоритмов)

На выборке ирисов Фишера *kwNN* показывает такую же эффективность, как и *kNN*. Поэтому, чтобы показать более высокую эффективность *kwNN*, подберём выборку вруную так, чтобы *kNN* ошибся при классификации, а *kwNN* - нет.
Приведём пример:

![kNN_vs_kwNN.png](https://github.com/IHappyPlant/RProjects/blob/master/img/kNN_vs_kwNN.png) 

При *k = 5* точку с координатами *(5; 5) kNN* отнёс с синему классу, так как синих объектов среди *k* ближайших соседей больше, а *kwNN* при *k = 5*, *q = 0.1*, отнёс её к классу красных объектов, так как они расположены ближе к точке, и влияют на классификацию сильнее.

### Метод парзеновского окна
Идея этого алгоритма в том, чтобы присваивать вес каждому объекту выборки не на основе ранга близости *i-го* объекта к классифицируемому, а на основе *расстояния* от классифицируемого объекта до данного объекта выборки. В отличии от метода ближайших соседей, сортировка объектов выборки по расстоянию здесь не нужна.

Функция веса выглядит следующим образом: ![](http://latex.codecogs.com/gif.latex?w%28i%2C%20z%29%20%3D%20K%28%5Cfrac%7B%5Crho%28z%2C%20x_%7Bi%7D%29%7D%7Bh%7D%29)  
Где *i* - номер объекта выборки, *z* - классифицируемый объект, *xi* - *i-й* объект выборки, *h* - ширина окна, *K* - функция ядра.

Функция ядра - произвольная чётная функция, невозрастающая на *[0, +inf)*. На практике применяются следующие функции ядра:
1.  ![](http://latex.codecogs.com/gif.latex?K%28r%29%20%3D%20P%28r%29%20%3D%20%5Cfrac%7B1%7D%7B2%7D%5B%7Cr%7C%20%3C%3D%201%5D) - Прямоугольное ядро
2.  ![](http://latex.codecogs.com/gif.latex?K%28r%29%20%3D%20T%28r%29%20%3D%20%281%20-%20%7Cr%7C%29%5B%7Cr%7C%3C%3D1%5D) - Треугольное ядро
3.  ![](http://latex.codecogs.com/gif.latex?K%28r%29%20%3D%20E%28r%29%20%3D%20%5Cfrac%7B3%7D%7B4%7D%281%20-%20r%5E%7B2%7D%29%5B%7Cr%7C%3C%3D1%5D) - Ядро Епанечникова
4.  ![](http://latex.codecogs.com/gif.latex?K%28r%29%20%3D%20Q%28r%29%20%3D%20%5Cfrac%7B15%7D%7B16%7D%281%20-%20r%5E%7B2%7D%29%5E%7B2%7D%5B%7Cr%7C%3C%3D1%5D) - Квартическое ядро
5.  ![](http://latex.codecogs.com/gif.latex?K%28r%29%20%3D%20G%28r%29%20%3D%20%282%5Cpi%29%5E%7B-%5Cfrac%7B1%7D%7B2%7D%7De%5E%7B%28-%5Cfrac%7B1%7D%7B2%7Dr%5E%7B2%7D%29%7D) - Гауссовское ядро

Где ![](http://latex.codecogs.com/gif.latex?r%3D%5Cfrac%7B%5Crho%28z%2C%20x_%7Bi%7D%29%7D%7Bh%7D)

#### Программная реализация алгоритма:

    parzen <- function(xl, h, distances, kernelFunction = kernel.G) {
      # xl - выборка
      # h - ширина окна
      # distances - расстояния от объекта z до каждого объекта из xl 
      # Расстояния считаются заранее, поэтому сам объект z здесь не нужен
      # kernelFunction - используемая функция ядра. По умолчанию Гауссовское
      l <- nrow(xl)
      n <- ncol(xl)
      classes <- xl[1:l, n] # Классы объектов выборки
      weights <- table(classes) # Таблица для весов классов
      weights[1:length(weights)] <- 0
      for (i in 1:l) { # Для каждого объекта выборки
        class <- xl[i, n] # Берём его класс
        r <- distances[i] / h
        weights[class] <- weights[class] + kernelFunction(r) # И прибавляем его вес к общему весу его класса
      }
      if (max(weights) != 0) # Если точке присвоились какие-нибудь веса классов (точка попала в окно)
	    return (names(which.max(weights))) # Вернуть класс с максимальным весом
	  return (0) # Иначе вернуть 0
    }
Значение *h* подбиралось в пределах от *0.1* до *2* по *LOO*. Алгоритм тестировался на выборке ирисов Фишера для разных функций ядра:

*Прямоугольное ядро:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/parzen_plot_P.png)
*Треугольное ядро:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/parzen_plot_T.png)
*Ядро Епанечникова:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/parzen_plot_E.png)
*Квартическое ядро:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/parzen_plot_Q.png)
*Гауссовское ядро:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/parzen_plot_G.png)

У всех ядер, кроме Гауссовского один и тот же недостаток: они не способны проклассифицировать точки, не попавшие в окно (те, для которых *|r| > 1*).  
Значение *h* имеет смысл подбирать в пределах небольших значений, т. к. при большом значении *h* слишком много точек попадает в окно, и качество классификации падает *крайне* значительно.  
Для примера приведём карту классификации для Гауссовского ядра при попытке выбора *h* по *LOO* в пределах от *1* до *l (длина выборки)*:
![](https://github.com/IHappyPlant/RProjects/blob/master/img/parzen_plot_badH.png)

Достоинства алгоритма:
1. Простота реализации
2. Высокое качество классификации при правильно подобранном *h*
3. Для классификации объекта не требуется сортировка выборки, что сокращает время классификации

Недостатки алгоритма:
1. Необходимость хранить выборку целиком
2. Малый набор параметров
3. Если ни один объект выборки не попал в радиус окна *h* вокруг объекта *z*, то алгоритм не способен его проклассифицировать. Исправляется использованием Гауссовского ядра и/или переменной ширины окна.

### Метод потенциальных функций
Метод получается, если поместить центр парзеновского окна не в классифицируемый объект, а в каждый объект обучающей выборки.  
Пусть дана обучающая выборка ![xl](http://latex.codecogs.com/gif.latex?%24X%5El%24) и объект *z*, который требуется классифицировать. Тогда:

1. Для каждого объекта выборки задаётся *ширина окна* ![h_i](http://latex.codecogs.com/gif.latex?h_%7Bi%7D) (выбирается из собственных соображений).
2. Для каждого объекта выборки задаётся *сила потенциала* ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D). Алгоритм подбора ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D) указан далее.
3. Каждому объекту выборки присваивается *вес* по формуле ![](http://latex.codecogs.com/gif.latex?w%28x_%7Bi%7D%2Cz%29%3D%5Cgamma_%7Bi%7DK%28%5Cfrac%7B%5Crho%28x%7Bi%7D%2Cz%29%7D%7Bh_%7Bi%7D%7D%29%3D%5Cgamma_%7Bi%7DK%28r%29), ![](http://latex.codecogs.com/gif.latex?K%28r%29) - функция ядра.
4. Суммируются веса объектов одинаковых классов. Класс с самым большим весом присваивается объекту *z*.

Алгоритм подбора ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D):
1. По умолчанию все ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D) задаются равными нулю. Задается максимально допустимое число ошибок.
2. Из обучающей выборки случайным образом, или последовательно выбирается объект ![x_i](http://latex.codecogs.com/gif.latex?x_%7Bi%7D).
3.  Для ![x_i](http://latex.codecogs.com/gif.latex?x_%7Bi%7D) запускается алгоритм классификации.
4. Если полученный класс не совпал с реальным, то *сила потенциала* для выбранного объекта увеличивается на 1. Иначе шаги 2-4 повторяются.
5. Алгоритм классификации с полученными значениями потенциалов запускается для каждого объекта выборки. Подсчитывается число ошибок.
6. Если число ошибок меньше заданного на шаге 1, то алгоритм завершает работу. Иначе повторяются шаги 2-6.

 Программная реализация метода потенциальных функций:
 
    pF <- function(distances, potentials, h, xl) {
      l <- nrow(xl)
      n <- ncol(xl)
      classes <- xl[, n]
      weights <- table(classes) # Таблица для весов классов
      weights[1:length(weights)] <- 0 # По умолчанию все веса равны нулю
      for (i in 1:l) { # Для каждого объекта выборки
        class <- xl[i, n] # Берется его класс
        r <- distances[i] / h[i]
        weights[class] <- weights[class] + potentials[i] * kernel.Q(r) # Считается его вес прибавляется к общему ввесу его класса
      }
      if (max(weights) != 0) return (names(which.max(weights))) # Если есть веса больше нуля, то вернуть класс с наибольшим весом
      return ("") # Если точка не проклассифицировалась, то вернуть пустую строку
    }
Программная реализация алгоритма подбора ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D):

    getPotentials <- function(xl, h, eps) {
      # Получить потенциалы всех объектов выборки
      l <- nrow(xl)
      n <- ncol(xl)
      potentials <- rep(0, l)
      err <- eps + 1
      # Пока число ошибок больше заданного
      while (err > eps) {
        while (TRUE) {
          # Пока не получим несоответствие классов, чтобы обновить потенциалы
          rand <- sample(1:l, 1)
          distances <- getDistances(xl, xl[rand, 1:(n-1)])
          class <- pF(distances, potentials, h, xl)
          if (class != xl[rand, n]) {
          potentials[rand] = potentials[rand] + 1
          break
          }
        }
        # Подсчет числа ошибок
        err <- 0
        for (i in 1:l) {
          distances <- getDistances(xl, xl[i, 1:(n-1)])
          class <- pF(distances, potentials, h, xl)
          err <- err + (class != xl[i, n])
        }
      }
      return (potentials)
    }
Алгоритм тестировался на выборке ирисов Фишера. Использовалось квартическое ядро. Радиусы потенциалов *h* для класса *setosa* были положены равными 1, так как класс далеко от остальных, а радиусы для *versicolor* и *virginica*  0.4, так как это число было оптимальным значением *h* для метода парзеновского окна.  
Результат работы:
![](https://github.com/IHappyPlant/RProjects/blob/master/img/pF_plot_rand.png)

Достоинства алгоритма:

1. Большое количество параметров для подбора.  
2.  После настройки силы потенциалов, объекты выборки с нулевыми потенциалами можно больше не хранить.

Недостатки алгоритма:

1. Слишком грубая настройка параметров ![h_i](http://latex.codecogs.com/gif.latex?h_%7Bi%7D) и ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D).
2. Неопределённое время работы алгоритма подбора ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D). 
3. При случайном выборе объектов из выборки при подборе ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D), результат работы на одной и той же выборке будет разным. Исправляется использованием эталонных объектов. 
4. Исходя из предыдущих пунктов - неопределённое качество классификации. Возможно, улучшаемое при использовании эталонных объектов.

