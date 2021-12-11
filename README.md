# Задача модифицированной каскадной схемы суммирования

Мой номер в списке: 3<br/>
Вариант: 1 + (3%4) = 4<br/>
Программа написана на языке С и считает каскадную сумму массива из 10 элементов 0..9

## Суть работы программы
* на первой итерации каскадной схемы все исходные данные разбиваются на пары, и для каждой пары вычисляется сумма их значений;
* далее все полученные суммы также разбиваются на пары, и снова выполняется суммирование значений пар и т.д

## Комментарии к коду
* Создание параллельных процессов
```
MPI_Init(&argc, &argv);
```
* Узнать идентификатор процесса и сколько процессов было запущено
```
MPI_Comm_rank(MPI_COMM_WORLD, &pid);
MPI_Comm_size(MPI_COMM_WORLD, &np);
```

* Распределение части массива дочерним процессам для вычисления их частичных сумм
```
for (i = 1; i < np - 1; i++) {
  index = i * elements_per_process;
  
  MPI_Send(&elements_per_process,
    1, MPI_INT, i, 0,
    MPI_COMM_WORLD);
  
  MPI_Send(&a[index],
    elements_per_process,
    MPI_INT, i, 0,
    MPI_COMM_WORLD);
}
```

* Последний процесс добавляет оставшиеся элементы
```
index = i * elements_per_process;
  int elements_left = n - index;
  
  MPI_Send(&elements_left,
    1, MPI_INT,
    i, 0,
    MPI_COMM_WORLD);
    
  MPI_Send(&a[index],
    elements_left,
    MPI_INT, i, 0,
    MPI_COMM_WORLD);
```

## Скомпилируйте и запустите программу, используя следующий код:
```
mpicc main.c -o object_file
mpirun -np [number of processes] ./object_file
```
## Вывод
```
Sum of array is : 45
```
