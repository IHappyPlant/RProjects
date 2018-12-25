












# Теория машинного обучения

## Навигация
* [Метрические алгоритмы классификации](#метрические-алгоритмы-классификации) 
	* [Сводная таблица](#сводная-таблица-по-метрическим-алгоритмам)
	* [Метод *k* ближайших соседей *(kNN)*](#метод-k-ближайших-соседей-knn)  
	* [Метод  *k*  взвешенных ближайших соседей *(kwNN)*](#метод-k-взвешенных-ближайших-соседей-kwnn)  
	* [Метод парзеновского окна](#метод-парзеновского-окна)  
	* [Метод потенциальных функций](#метод-потенциальных-функций)  
	* [STOLP](#алгоритм-stolp)
* [Байесовские алгоритмы классификации](#байесовские-алгоритмы-классификации)
	* [Нормальный дискриминантный анализ](#нормальный-дискриминантный-анализ)
	* [Наивный байесовский классификатор](#наивный-байесовский-классификатор)
	* [Подстановочный (Plug-in) алгоритм](#подстановочный-plug-in-алгоритм)
	* [Линейный дискриминант Фишера](#линейный-дискриминант-фишера)
	* [Сравнение Plug-in и ЛДФ](#сравнение-plug-in-и-лдф)
* [Линейные алгоритмы классификации](#линейные-алгоритмы-классификации)
	* [Метод стохастического градиента](#метод-стохастического-градиента)
	* [Метод ADALINE](#метод-adaline)
	* [Правило Хебба](#правило-хебба-персептрон-розенблатта)
	* [Логистическая регрессия](#логистическая-регрессия)
	* [Сравнение линейных классификаторов](#сравнение-линейных-классификаторов)
## Метрические алгоритмы классификации
Метрические алгоритмы классификации - алгоритмы, основанные на вычислении оценок сходства между объектами. Одним из вариантов сходства является расстояние между объектами. Тогда вводится функция расстояния ![](http://latex.codecogs.com/gif.latex?%24%5Crho%20%28x_1%2C%20x_2%29%24).

### Сводная таблица по метрическим алгоритмам
| Алгоритм              | Параметры                                            | LOO   | Кол-во ошибок |
|-----------------------|------------------------------------------------------|-------|---------------|
| [STOLP-kwNN](#алгоритм-stolp)|k = 20, q = 0.1; после отбора эталонов k = 8, q = 0.1| STOLP сам по себе вместо LOO | 3 |
| [kNN](#метод-k-ближайших-соседей-knn)                   | k = 6                                                | 0.03 | 5          |
| [kwNN](#метод-k-взвешенных-ближайших-соседей-kwnn)                  | k = 6, q = 1                                       | 0.03 | 5          |
| [Потенциальные функции](#метод-потенциальных-функций) | Ядро - квартическое, h(setosa) = 1, h(!setosa) = 0.4, Gamma_i - случайные | вместо LOO - подбор Gamma_i     | 5             |
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
````r
kNN <- function(xl, z, k) names(which.max(table(sort_objects_by_dist(xl, z)[1:k, ncol(xl)])))
````
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
````r
w.kwnn <- function(i, k, q) (i <= k) * q^i # Весовая функция
kwNN <- function(xl, z, k, q) {
  ordered_xl <- sort_objects_by_dist(xl, z)
  weights <- w.kwnn(1:nrow(ordered_xl), k, q)
  names(weights) <- ordered_xl[, ncol(ordered_xl)]
  sum_by_class <- sapply(unique(sort(names(weights))), function(class, weights) sum(weights[names(weights) == class]), weights)
  names(which.max(sum_by_class))
}
````
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
````r
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
````
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
````r
pF <- function(distances, potentials, h, xl, kernelFunction = kernel.G) {
  l <- nrow(xl)
  n <- ncol(xl)
  classes <- xl[, n]
  weights <- table(classes) # Таблица для весов классов
  weights[1:length(weights)] <- 0 # По умолчанию все веса равны нулю
  for (i in 1:l) { # Для каждого объекта выборки
    class <- xl[i, n] # Берется его класс
    r <- distances[i] / h[i]
    weights[class] <- weights[class] + potentials[i] * kernelFunction(r) # Считается его вес, и прибавляется к общему ввесу его класса
  }
  if (max(weights) != 0) return (names(which.max(weights))) # Если есть веса больше нуля, то вернуть класс с наибольшим весом
  return ("") # Если точка не проклассифицировалась, то вернуть пустую строку
}
````
Программная реализация алгоритма подбора ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D):
````r
getPotentials <- function(xl, h, eps, kernelFunction = kernel.G) {
  # Получить потенциалы всех объектов выборки
  l <- nrow(xl)
  n <- ncol(xl)
  potentials <- rep(0, l)
  err <- eps + 1
  # Посчитаем расстояния от каждого объекта выборки до остальных
  distances <- matrix(0, l, l)
  for (i in 1:l)
    distances[i,] <- getDistances(xl, c(xl[i, 1], xl[i, 2]))
  # Пока число ошибок больше заданного
  while (err > eps) {
    while (TRUE) {
      # Пока не получим несоответствие классов, чтобы обновить потенциалы
      rand <- sample(1:l, 1)
      class <- pF(distances[rand,], potentials, h, xl)
      if (class != xl[rand, n]) {
        potentials[rand] = potentials[rand] + 1
        break
      }
    }
    # Подсчет числа ошибок
    err <- 0
    for (i in 1:l) {
      class <- pF(distances[i,], potentials, h, xl)
      err <- err + (class != xl[i, n])
    }
  }
  return (potentials)
}
````
Алгоритм тестировался на выборке ирисов Фишера. Использовалось квартическое ядро. Радиусы потенциалов *h* для класса *setosa* были положены равными 1, так как класс далеко от остальных, а радиусы для *versicolor* и *virginica*  0.4, так как это число было оптимальным значением *h* для метода парзеновского окна.  
Результат работы:
![](https://github.com/IHappyPlant/RProjects/blob/master/img/pF_plot_rand_v2.png)

Достоинства алгоритма:

1. Большое количество параметров для подбора.  
2. После настройки силы потенциалов, объекты выборки с нулевыми потенциалами можно не использовать при классификации. Благодаря этому - высокая скорость работы.

Недостатки алгоритма:

1. Слишком грубая настройка параметров ![h_i](http://latex.codecogs.com/gif.latex?h_%7Bi%7D) и ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D).
2. Неопределённое время работы алгоритма подбора ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D). 
3. При случайном выборе объектов из выборки при подборе ![gamma_i](http://latex.codecogs.com/gif.latex?%5Cgamma_%7Bi%7D), результат работы на одной и той же выборке будет разным.
4. Исходя из предыдущих пунктов - неопределённое качество классификации.

### Алгоритм STOLP
Введём понятие *отступа*. Отступ - величина, показывающая, насколько объект является типичным представителем своего класса. Отступ равен разности между степенью близости объекта к своему классу и суммой близостей объекта ко всем остальным классам.  
![](http://latex.codecogs.com/gif.latex?M%28x_%7Bi%7D%29%3DW_%7By_%7Bi%7D%7D%28x_%7Bi%7D%2CX%5E%7Bl%7D%29-%5Cmax%20W_%7By%7D%28x_%7Bi%7D%2C%20X%5E%7Bl%7D%29)  
Все объекты обучающей выборки можно разделить на несколько типов:

1. Эталонные объекты - наиболее типичные представители своего класса
2. Неинформативные - объекты, не влияющие значительным образом на качество классификации
3. Пограничные - объекты, имеющие отступ, близкий к нулю. Незначительное изменение в выборке, алгоритме, метрике и т.п. может повлиять на их классификацию.
4. Ошибочные - объекты с отрицательными отступами, классифицируемые неверно.
5. Шумовые объекты (выбросы) - малая группа объектов с большими отрицательными отступами. Их удаление улучшает качество классификации.

Идея алгоритма STOLP состоит в том, чтобы уменьшить исходную выборку, выбрав из неё эталонные объекты. Такой подход уменьшит размер выборки, и может улучшить качество классификации.  На вход подаётся выборка, допустимый порог ошибок и порог фильтрации выбросов. Алгоритм:  

1. Удалить из выборки все выбросы (объекты, отступ которых меньше порога фильтрации выбросов).
2. Пересчитать все отступы заново, взять по одному эталону из каждого класса (объекты с наибольшим положительным отступом), и добавить их в множество эталонов.
3. Проклассифицировать объекты обучающей выборки, взяв в качестве обучающей выборки для этого множество эталонов. Посчитать число ошибок.
4. Если число ошибок меньше заданного порога, то алгоритм завершается.
5. Иначе присоединить ко множеству эталонов объекты с наименьшим отступом из каждого класса из числа классифицированных неправильно.
6. Повторять шаги 3-5 до тех пор, пока множество эталонов и обучающая выборка не совпадут, или не сработает проверка в пункте 4.

Протестируем алгоритм на выборке ирисов Фишера.  
В качестве алгоритма классификации будем использовать kwNN(k = 20, q = 0.1)  
Реализация функции отступа на основе kwNN:
````r
margin <- function(xl, z, k, q, target_class) {
  ordered_xl <- sort_objects_by_dist(xl, z) # Сортировка выборки
  weights <- w.kwnn(1:nrow(ordered_xl), k, q) # Веса объектов выборки
  names(weights) <- ordered_xl[, ncol(ordered_xl)]
  sum(weights[names(weights) == target_class]) - sum(weights[names(weights) != target_class]) # Отступ
}
````
Результат работы STOLP:  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/STOLP_plot_kwnn.png)
На оригинальной выборке kwNN с такими параметрами отработал с 7 ошибками, а на выборке из эталонов - всего 3. Выборка эталонов составила 8 объектов, значит, выборка уменьшилась 18.75 раз.

Достоинства алгоритма:

1. Значительно сокращает размер выборки
2. Улучшает качество классификации
3. Ускоряет классификацию

Недостатки алгоритма:

1. Сложность реализации

## Байесовские алгоритмы классификации
Байесовские алгоритмы классификации основаны на идее о том, что обучающая выборка формируется из неизвестного вероятностного распределения, имеющего некоторую плотность.  
В основе байесовского подхода лежит теорема о том, что если для классов известны вероятности *P* и плотности распределения *p*, то можно записать решающее правило в виде  
![](http://latex.codecogs.com/gif.latex?a%28x%29%20%3D%20%5Carg%5Cmax_%7By%20%5Cin%20Y%7D%20%5Clambda_%7By%7D%20P_%7By%7Dp_%7By%7D%28x%29), где ![](http://latex.codecogs.com/gif.latex?%5Clambda_%7By%7D) - важность класса *y*,  
и оно будет оптимальным, т. е. обладать наименьшей вероятностью ошибки.  
На практике вероятности и плотности распределения для классов обычно неизвестны, и их приходится восстанавливать по выборке. Из за этого решающее правило перестаёт быть оптимальным, так как восстановить эти значения по выборке можно только с некоторой погрешностью.  
Существуют параметрический и не параметрический подходы к задаче восстановления плотности.  

При параметрическом подходе плотность распределения выборки ![](http://latex.codecogs.com/gif.latex?X%5E%7Bm%7D)  полагается известной с точностью до параметра ![](http://latex.codecogs.com/gif.latex?%5Ctheta).  
### Нормальный дискриминантный анализ
Это метод классификации, основанный на параметрическом подходе, специальный случай, при котором плотности распределения всех классов полагаются многомерными нормальными.  
Функция плотности многомерного нормального распределения выглядит следующим образом:  
![](http://latex.codecogs.com/gif.latex?N%28x%2C%20%5Cmu%2C%20%5CSigma%29%3D%5Cfrac%7B1%7D%7B%5Csqrt%28%282%5Cpi%29%5E%7Bn%7D%7C%5CSigma%7C%29%7De%5E%7B-%5Cfrac%7B1%7D%7B2%7D%28%28x-%5Cmu%29%5CSigma%5E%7B-1%7D%28x-%5Cmu%29%5E%7BT%7D%29%7D), где  
![](http://latex.codecogs.com/gif.latex?x%20%5Cin%20R%5E%7Bn%7D) - объект, состоящий из *n* признаков  
![](http://latex.codecogs.com/gif.latex?%5Cmu%20%5Cin%20R%5E%7Bn%7D) - мат. ожидание каждого признака  
![](http://latex.codecogs.com/gif.latex?%5CSigma%20%5Cin%20R%5E%7Bn%5Ctimes%20n%7D) - матрица ковариации признаков. Симметричная, невырожденная, положительно определённая.  
#### Линии уровня нормального распределения
1. Если признаки некоррелированы, т. е. матрица ковариации диагональна, то линии уровня имеют форму эллипсоидов, параллельных осям координат, вытянутых относительно признака, значение для которого в матрице выше.
![](https://github.com/IHappyPlant/RProjects/blob/master/img/level_lines_eq.png)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/level_lines_a1.png)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/level_lines_a2.png)

2. Если признаки коррелированы, то есть матрица ковариации не диагональна, то линии уровня имеют форму эллипсоидов, наклонённых относительно осей координат.
![](https://github.com/IHappyPlant/RProjects/blob/master/img/level_lines_a12_plus.png)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/level_lines_a12_minus.png)  
Сама программа, рисующая линии уровня представлена [здесь](https://ihappyplant.shinyapps.io/level_lines/)

### Наивный байесовский классификатор
Если предположить, что все признаки объектов выборки сформированы независимо, то значение функции плотности ![](http://latex.codecogs.com/gif.latex?p_%7By%7D%28x%29) для класса *y* для объекта *x* можно представить в виде ![](http://latex.codecogs.com/gif.latex?p_%7By%7D%28x%29%3Dp_%7By%7D%28%5Cxi_%7B1%7D%29*%5Cdots*p_%7By%7D%28%5Cxi_%7Bn%7D%29), где ![](http://latex.codecogs.com/gif.latex?%5Cxi_%7Bi%7D) - *i*-й признак объекта *x*. Это упрощает задачу, так как оценивать несколько одномерных плотностей легче, чем одну многомерную.  
Однако, на практике такая ситуация встречается редко, поэтому алгоритм получил название "Наивный байесовский классификатор". Обычно он используется, как эталон при сравнении различных алгоритмов классификации.  
Решающее правило принимает вид:  
![](http://latex.codecogs.com/gif.latex?a%28x%29%3Darg%20%5Cmax_%7By%5Cin%20Y%7D%28%5Cln%28%5Clambda_%7By%7DP_y%29&plus;%5Csum_%7Bj%3D1%7D%5E%7Bn%7D%5Cln%28p_%7Byj%7D%28%5Cxi_j%29%29%29)  
Примеры работы алгоритма:
![](https://github.com/IHappyPlant/RProjects/blob/master/img/naive_bayes1.PNG)

![](https://github.com/IHappyPlant/RProjects/blob/master/img/naive_bayes2.PNG)  

В приведённых примерах все признаки действительно взяты независимо, поэтому алгоритм показывает хорошее качество классификации. В практических задачах результат был бы намного хуже.

Достоинства алгоритма:
1. Простота реализации.
2. Низкие вычислительные затраты при обучении и классификации.
3. Если признаки (почти) независимы, то алгоритм (почти) оптимален.

Недостатки алгоритма:
1. В общем случае - низкое качество классификации.

Приложение, реализующее алгоритм, представлено [здесь](https://ihappyplant.shinyapps.io/bayes_classifiers/).

### Подстановочный (Plug-in) алгоритм
Алгоритм относится к нормальному дискриминантному анализу и применяется для многомерных (в данном случае - для многомерного нормального) распределений.  
Решающее правило имеет вид:
![](http://latex.codecogs.com/gif.latex?a%28x%29%3Darg%5Cmax_%7By%5Cin%20Y%7D%5Clambda_y%5Chat%20P_y%5Chat%20p_y%28x%29), где ![](http://latex.codecogs.com/gif.latex?%5Chat%20P_y%2C%5Chat%20p_y%28x%29) - восстановленные вероятность и плотность распределения класса соответственно.  

![](http://latex.codecogs.com/gif.latex?%5Chat%20%5Cmu%28x%29%20%3D%20%5Cfrac%7B1%7D%7Bm%7D%5Csum%5Em_%7Bi%3D1%7Dx_i)  
![](http://latex.codecogs.com/gif.latex?%5Chat%20p_y%28x%29%20%3D%20N%28x%2C%20%5Cmu%2C%20%5CSigma%29%20%3D%20%5Cfrac%7B1%7D%7B%5Csqrt%28%282%5Cpi%29%5En%7C%5CSigma%7C%29%7D%5Cexp%28-%5Cfrac%7B1%7D%7B2%7D%28x-%5Cmu%29%5CSigma%5E%7B-1%7D%28x-%5Cmu%29%5ET%29)  
где ![](http://latex.codecogs.com/gif.latex?%5CSigma) - восстановленная матрица ковариации.
Матрица ковариации вычисляется по формуле:  
![](http://latex.codecogs.com/gif.latex?%5CSigma%28x%2C%20%5Cmu%29%20%3D%20%5Cfrac%7B1%7D%7Bm-1%7D%5Csum%5Em_%7Bi%3D1%7D%28x_i-%5Cmu%29%28x_i-%5Cmu%29%5ET)  
 
 Восстановление мат. ожидания:
````r
get_mu <- function(xm) colMeans(xm)
````
Восстановление матрицы ковариации:
````r
get_sigma <- function(xm, mu) {
  sum <- 0
  for (i in 1:nrow(xm)) {
    xi <- matrix(c(xm[i,1], xm[i,2]), 1, 2)
    sum <- sum + t(xi - mu) %*% (xi - mu)
  }
  sum / (nrow(xm)-1)
}
````
Полученные матрицу ковариации и вектор мат. ожиданий подставляют в формулу плотности, которую, в свою очередь, подставляют в решающее правило. Поэтому алгоритм получил название "Подстановочный".  

Алгоритм позволяет построить разделяющую кривую между классами, решив уравнение:  
![](http://latex.codecogs.com/gif.latex?%5Clambda_1%5Chat%20P_1%5Chat%20p_1%28x%29%3D%5Clambda_2%5Chat%20P_2%20%5Chat%20p_2%28x%29)  
Это уравнение, при раскрытии переменных, превратится в уравнение кривой второго порядка, при подстановке точек, в которое, получим разделяющую кривую между классами.

Примеры работы алгоритма:
![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin1.PNG)

![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin2.PNG)

![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin3.PNG)

Приложение, реализующее алгоритм, представлено [здесь](https://ihappyplant.shinyapps.io/bayes_classifiers/).

### Линейный дискриминант Фишера
ЛДФ является частным случаем подстановочного алгоритма, для случая, когда матрицы ковариации всех классов равны.  
В этом случае матрицу ковариации можно восстановить для всех классов сразу, по формуле:
![](http://latex.codecogs.com/gif.latex?%5CSigma%28x%2C%20%5Cmu%29%20%3D%20%5Cfrac%7B1%7D%7Bm%20-%20%7CY%7C%7D%5Csum%5Em_%7Bi%3D1%7D%28x_i-%5Chat%20%5Cmu_%7Byi%7D%29%28x_i-%5Chat%20%5Cmu_%7Byi%7D%29%5ET)  
где *|Y|* - мощность множества классов, *x* - объекты выборки.
Программная реализация:
````r
get_sigma <- function(xm, mu) {
  sum <- 0
  m <- nrow(xm)
  for (i in 1:(m/2)) {
    xi <- matrix(c(xm[i,1], xm[i,2]), 1, 2)
    sum <- sum + t(xi - mu[1,]) %*% (xi - mu[1,])
  }
  for (i in (m/2+1):m) {
    xi <- matrix(c(xm[i,1], xm[i,2]), 1, 2)
    sum <- sum + t(xi - mu[2,]) %*% (xi - mu[2,])
  }
  sum / (m-2)
}
````

Уравнение разделяющей поверхности упрощается, и превращается из уравнения кривой второго порядка в уравнение прямой.

Примеры работы алгоритма:
![](https://github.com/IHappyPlant/RProjects/blob/master/img/ldf1.PNG)

![](https://github.com/IHappyPlant/RProjects/blob/master/img/ldf2.PNG)

Приложение, реализующее алгоритм, представлено [здесь](https://ihappyplant.shinyapps.io/bayes_classifiers/).

### Сравнение Plug-in и ЛДФ
Очевидно, что подстановочный алгоритм покрывает больше случаев, и является намного более универсальным, чем ЛДФ.  
Но ЛДФ создавался специально для работы с особыми случаями, а именно - когда матрицы ковариации классов равны.  

Сравним работу Plug-in и ЛДФ на таких данных:  
*Plug-in:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin_vs_ldf1.PNG)  
*ЛДФ:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/ldf1.PNG)  
*Plug-in:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin_vs_ldf2.PNG)  
*ЛДФ:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/ldf2.PNG)  
*Plug-in:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin_vs_ldf3.PNG)  
*ЛДФ:*
![](https://github.com/IHappyPlant/RProjects/blob/master/img/plugin_vs_ldf4.PNG)  

Вывод: для случая, когда матрицы ковариации классов равны, и Plug-in и ЛДФ показывают схожие результаты, что неудивительно, т. к. ЛДФ - частный случай Plug-in. Тем не менее, ЛДФ значительно проще в реализации, и требует меньше вычислительной мощности, благодаря более простой формуле. Также, в случае, когда классы накладываются друг на друга, Plug-in "ломается", а ЛДФ - нет.

## Линейные алгоритмы классификации
Рассмотрим задачу классификации с множеством объектов ![](http://latex.codecogs.com/gif.latex?X%3D%5Cmathbb%7BR%7D%5En) и множеством ответов *Y = {-1, +1}*.  
Рассмотрим модель алгоритма ![](http://latex.codecogs.com/gif.latex?a%28x%2Cw%29%3Dsignf%28x%2Cw%29), где *w* - вектор параметров, а *f(x, w)* - дискриминантная функция.  
Если *f(x, w) > 0*, то алгоритм отнесёт объект к классу *+1*, иначе - к классу *-1*.   
Уравнение *f(x, w) = 0* задаёт разделяющую поверхность.  
Если ![](http://latex.codecogs.com/gif.latex?f%28w%2Cx%29%3D%5Clangle%20w%2C%20x%5Crangle%20-w_0), то алгоритм классификации называется *линейным*, и решающее правило принимает вид:  
![](http://latex.codecogs.com/gif.latex?a%28x%2Cw%29%3Dsign%20%28%5Clangle%20w%2Cx%5Crangle%20-w_0%29%3Dsign%28%5Csum%5En_%7Bi%3D1%7Dw_if_i%28x%29-w_0%29)  

Обучение состоит в минимизации аппроксимированного эмпирического риска:  
![](http://latex.codecogs.com/gif.latex?Q%28w%2C%20X%5El%29%20%3D%20%5Csum%5El_%7Bi%3D1%7D%5Cpounds%20%28M%29) , где ![](http://latex.codecogs.com/gif.latex?%5Cpounds%20%28M%29) - функция потерь, *M* - отступ  
и заключается в подборе компонент вектора *w*.  

Для минимизации *Q(w)* применим модифицированный *метод градиентного спуска*.  
### Метод стохастического градиента
Суть метода:  выбирается начальное приближение вектора *w*, и запускается итерационный процесс, при котором на каждом шаге вектор *w* изменяется в направлении наиболее быстрого убывания *Q*. Это направление противоположно направлению вектора градиента ![](http://latex.codecogs.com/gif.latex?Q%27%3D%28%5Cfrac%7B%5Cpartial%20Q%28w%29%7D%7B%5Cpartial%20w_j%7D%29%5En_%7Bj%3D1%7D): ![](http://latex.codecogs.com/gif.latex?w%3Dw-%5Ceta%20Q%27%28w%29), где ![](http://latex.codecogs.com/gif.latex?%5Ceta) - *темп обучения*. В этом методе прецеденты (пары "объект-ответ") для пересчёта *w* выбираются случайно, поэтому метод получил название "Метод стохастического градиента".  
Алгоритм:  
1. Инициализировать вектор *w*.
2. Вычислить начальное значение функционала ![](http://latex.codecogs.com/gif.latex?Q%3D%5Csum%5El_%7Bi%3D1%7D%5Cpounds%28%5Clangle%20w%2C%20x_i%5Crangle%20y_i%29).
3. Выбрать из выборки объект ![](http://latex.codecogs.com/gif.latex?x_i).
4. Вычислить ошибку алгоритма на выбранном объекте: ![](http://latex.codecogs.com/gif.latex?%5Cvarepsilon_i%20%3D%20%5Cpounds%20%28%5Clangle%20w%2C%20x_i%5Crangle%20y_i%29).
5. Пересчитать значения ![](http://latex.codecogs.com/gif.latex?w%3Dw-%5Ceta%20%5Cpounds%5E%60%28%3Cw%2Cx_i%3Ey_i%29x_iy_i).  
6. Пересчитать значение ![](http://latex.codecogs.com/gif.latex?Q%3D%281-%5Clambda%29Q&plus;%5Clambda%20%5Cvarepsilon).
7. Повторять п.3 - п.6 до тех пор, пока значение *Q*  и/или вектор *w* не перестанут изменятся.

Программная реализация:
````r
gradient <- function(xl, eta, lambda, rule, loss_function, eps_q, stop_by_margins) {
  l <- nrow(xl)
  n <- ncol(xl)
  w <- matrix(c(runif(n-1, -1/(2*(n-1)), 1/(2*(n-1)))), 1, 3) # Инициализация w случайными значениями
  objects <- xl[,-n]
  classes <- xl[, n]
  q <- sum(sapply(1:l, function(i) loss_function(margin(w, objects[i,], classes[i])))) # Начальная инициализация q
  q_full <- matrix(q, 1, 1)
  cnt <- 0
  while (T) {
    cnt <- cnt + 1 # Счётчик итераций
    margins <- sapply(1:l, function(i) margin(w[cnt,], objects[i,], classes[i]))
    errors <- which(margins < 0)
    
    if (length(errors) == 0 && stop_by_margins == T) break;
    
    if (length(errors) > 0) rand <- sample(errors, 1)
    else rand <- sample(1:l, 1)
    
    eps <- loss_function(margin(w[cnt,], objects[rand,], classes[rand])) # Ошибка алгоритма на объекте
    
    eta <- 1 / (objects[rand,] %*% objects[rand,])^2 # Пересчёт темпа обучения
    if (length(errors) == 0) eta <- eta / 2
    
    # Обновление весов
    w <- rbind(w, rule(w[cnt,], objects[rand,], classes[rand], eta))
    
    # Пересчёт q
    q_prev <- q
    q <- (1 - lambda) * q + lambda * eps
    q_full <- rbind(q_full, q)
    
    if (abs(q_prev - q) / max(q_prev, q) <= eps_q) { print("exit by q"); break; }
    else if (cnt == 20000) { print("exit by cnt"); break; }
    
  }
  w <- cbind(w, q_full)
  w
}
````
Преимущества метода:
1. Простота реализации.
2. Метод легко обобщается и на нелинейные классификаторы.
3. Позволяет быстро настраивать веса даже на избыточно больших выборках.

Недостатки:
1. Неустойчивая сходимость (может сойтись к локальному экстремуму, может сходится очень медленно, а может вообще не сходится).
2. При большой размерности пространства ![](http://latex.codecogs.com/gif.latex?%5Cmathbb%7BR%7D%5En) или малой длине выборки возможно переобучение. Тогда классификация становится неустойчивой.
### Метод ADALINE
Возьмём ![](http://latex.codecogs.com/gif.latex?%5Cpounds%28M%29%3D%28M-1%29%5E2). Вычислив производную по *w*, получим: ![](http://latex.codecogs.com/gif.latex?%5Cpounds%27%28M%29%3D2%28%5Clangle%20w%2C%20x_i%5Crangle%20-%20y_i%29x_i)  .  
Получим правило обновления весов на каждой итерации стохастического градиента: ![](http://latex.codecogs.com/gif.latex?w%20%3D%20w-%5Ceta%28%5Clangle%20w%2C%20x_i%5Crangle%20-%20y_i%29x_i).  
Это правило называется дельта-правилом и реализуется программно следующим образом:  
````r
adaline.get_w <- function(w, object, class, eta)  w - c(eta) * (w %*% object - class) %*% object
````
Примеры работы классификатора, обученного методом стохастического градиента с применением дельта-правила:  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/adaline1.PNG)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/adaline2.PNG)  
Приложение, реализующее ADALINE, представлено [здесь](https://ihappyplant.shinyapps.io/linear_classifiers/).

### Правило Хебба (персептрон Розенблатта)
Этот принцип обучения классификатора основан на принципах нейрофизиологии. Предполагается, что если нейрон угадывает правильный ответ, то его синапсы усиливаются, а если он часто ошибается, или вообще не используется, то синапсы начинают затухать.  
В приложении к этого к классификатору роль синапсов играет вектор *w*, и правило его обновления формализуется следующим образом: ![](http://latex.codecogs.com/gif.latex?if%20%28%5Clangle%20w%2Cx_i%5Crangle%20y_i%29%20%3C%200%20%5C%3Bthen%20%5C%3B%20w%3A%3Dw&plus;%5Ceta%20x_i%20y_i)  
Функция потерь полагается кусочно-линейной: ![](http://latex.codecogs.com/gif.latex?%5Cpounds%20%28M%29%3D%28-M%29_%7B&plus;%7D)  
Примеры работы персептрона:  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/hebb1.PNG)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/hebb2.PNG)  
Приложение, реализующее метод, представлено [здесь](https://ihappyplant.shinyapps.io/linear_classifiers/).  
### Логистическая регрессия
Является одновременно линейным и байесовским классификатором. Использует логарифмическую (логистическую) функцию потерь: ![](http://latex.codecogs.com/gif.latex?%5Cpounds%28M%29%3D%5Clog_2%281&plus;e%5E%7B-M%7D%29).  
Алгоритм назван так потому, что обычно для его обучения применяется не метод стохастического градиента, а метод Ньютона-Рапсона, в котором решается несколько задач восстановления регрессии.
Правило обновления весов: ![](http://latex.codecogs.com/gif.latex?w%3Dw&plus;%5Ceta%20x_iy_i%5Csigma%28%5Clangle%20w%2Cx_i%5Crangle%20y_i%29), где ![](http://latex.codecogs.com/gif.latex?%5Csigma%28z%29%3D%5Cfrac%7B1%7D%7B1&plus;e%5E%7B-z%7D%7D) - сигмоидная функция. С вероятностной точки зрения, ![](http://latex.codecogs.com/gif.latex?%5Csigma%28z%29) вычисляет апостериорную веростность ![](http://latex.codecogs.com/gif.latex?P%28y%7Cx%29).  
Пример классификации методом логистической регрессии:  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/logistic1.PNG)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/logistic2.PNG)  
Достоинства метода:  
1. Как правило, даёт более качественный результат по сравнению с ЛДФ, т. к. основано на менее жестких предположениях, а так же по сравнению с  ADALINE и правилом Хебба, т. к. использует более плавную функцию потерь.  
2. Возможность оценивания апостериорной вероятности.  

Недостатки метода:  
1. Если не выполняются предположения теоремы, то качество работы может ухудшиться.  

Приложение, реализующее метод логистической регрессии, представлено [здесь](https://ihappyplant.shinyapps.io/linear_classifiers/)  

### Сравнение линейных классификаторов
Сравним работу всех трёх вышеприведённых классификаторов. Не будем иллюстрировать процесс схождения, чтобы не перегружать картинку, покажем только конечный результат:  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/linear_all1.PNG)  
![](https://github.com/IHappyPlant/RProjects/blob/master/img/linear_all2.PNG)  
Вывод: ADALINE и Логистическая регрессия выигрывают в точности у правила Хебба, когда выборка линейно разделима. Но когда выборка плохо разделима, или не разделима, ADALINE проигрывает Логистической регрессии и правилу Хебба. Следовательно, алгоритм классификации для конкретной задачи в зависимости от исходных данных.  

### Support Vector Machine (метод опорных векторов)

[В начало](#теория-машинного-обучения)