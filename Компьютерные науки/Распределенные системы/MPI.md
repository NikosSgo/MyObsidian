Message passing interface (интерфейс проброса сообщений) - это стандартизированное API для реализации взаимодействия **идентичных узлов**, но имеющих при этом **раздельную памятью**. 

**SPMD — Single Program Multiple Data**
Ключевые идея взаимодействия:
- Одинаковы узлы
- Разделённая память



#### Общие параметры для всех методов:
- buf - массив данных (int[], double[], byte[], Object и т.д.)
- offset - начальный индекс в массиве (обычно 0)
- count - количество элементов для передачи
- datatype - тип MPI: MPI.INT, MPI.DOUBLE, MPI.FLOAT, MPI.LONG, MPI.CHAR, MPI.BYTE, MPI.BOOLEAN, MPI.OBJECT
- dest - ранг процесса-получателя
- source - ранг процесса-отправителя
- tag - целочисленный тег сообщения
- root - ранг корневого процесса в коллективных операциях
- op - операция редукции: MPI.SUM, MPI.PROD, MPI.MAX, MPI.MIN, MPI.LAND, MPI.LOR, MPI.BAND, MPI.BOR
- request - объект MPI.Request для неблокирующих операций
- status - объект MPI.Status для информации о завершении

#### Базовые методы управления средой:

- MPI.Init(String[] args) - инициализация среды MPI
- MPI.Finalize() - завершение работы с MPI
- MPI.COMM_WORLD.Rank() - получение ранга текущего процесса
- MPI.COMM_WORLD.Size() - получение общего числа процессов
- MPI.Wtime() - получение времени в секундах (для замера производительности)


#### Класс 1: Point-to-Point (блокирующие операции)

Эти методы блокируют выполнение программы до завершения операции. Send-методы не завершатся, пока данные не будут отправлены или скопированы в буфер. Recv блокирует выполнение до получения сообщения.

- Стандартная отправка (поведение зависит от реализации, может быть синхронной или буферизованной)
  MPI.COMM_WORLD.Send(buf, offset, count, datatype, dest, tag)
- Синхронная отправка (возврат только после того, как получатель начал прием)
  MPI.COMM_WORLD.Ssend(buf, offset, count, datatype, dest, tag)
- Буферизованная отправка (немедленный возврат, требует предварительного вызова MPI.Buffer_attach)
  MPI.COMM_WORLD.Bsend(buf, offset, count, datatype, dest, tag)
- Готовая отправка (без проверок, получатель ОБЯЗАН уже вызвать Recv, иначе ошибка)
  MPI.COMM_WORLD.Rsend(buf, offset, count, datatype, dest, tag)
- Прием сообщения (блокируется до получения данных)
  MPI.COMM_WORLD.Recv(buf, offset, count, datatype, source, tag)
- Специальные константы для source и tag
- MPI.ANY_SOURCE - принять от любого отправителя
- MPI.ANY_TAG - принять с любым тегом


#### Класс 2: Point-to-Point (неблокирующие операции)

Все неблокирующие методы возвращают объект MPI.Request. Это позволяет программе продолжать вычисления, пока передача данных выполняется асинхронно. Завершение операции проверяется методами Wait (блокирующий) или Test (неблокирующий).

- Стандартная неблокирующая отправка
  MPI.COMM_WORLD.Isend(buf, offset, count, datatype, dest, tag)
- Синхронная неблокирующая отправка
  MPI.COMM_WORLD.Issend(buf, offset, count, datatype, dest, tag)
- Буферизованная неблокирующая отправка
  MPI.COMM_WORLD.Ibsend(buf, offset, count, datatype, dest, tag)
- Готовая неблокирующая отправка
  MPI.COMM_WORLD.Irsend(buf, offset, count, datatype, dest, tag)
- Неблокирующий прием
  MPI.COMM_WORLD.Irecv(buf, offset, count, datatype, source, tag)
- Методы управления объектом MPI.Request
- request.Wait() - блокирует до завершения операции, возвращает MPI.Status
- request.Test() - неблокирующая проверка, возвращает true если завершена
- request.Cancel() - отмена операции (не гарантирует немедленную остановку)
- MPI.Request.WaitAll(requests) - ожидает завершения всех переданных запросов
- MPI.Request.WaitAny(requests) - ожидает завершения любого из запросов, возвращает статус
- MPI.Request.TestAll(requests) - проверяет все запросы, возвращает true если все завершены
- MPI.Request.TestAny(requests) - проверяет любой запрос, возвращает индекс завершенного


#### Класс 3: Коллективные операции (вызываются ВСЕМИ процессами)

Коллективные операции вовлекают все процессы в коммуникаторе. Каждый процесс обязан вызвать метод с одинаковыми параметрами (кроме буферов, где это явно указано). Нарушение этого правила приводит к зависанию или ошибке.

- Барьерная синхронизация (все процессы ждут друг друга)
  MPI.COMM_WORLD.Barrier()
- Рассылка (Broadcast) - процесс с рангом root рассылает данные всем остальным
  MPI.COMM_WORLD.Bcast(buf, offset, count, datatype, root)
- Редукция (Reduce) - все процессы отправляют данные, root получает агрегированный результат
  MPI.COMM_WORLD.Reduce(sendBuf, recvBuf, offset, count, datatype, op, root)
- Редукция с рассылкой (Allreduce) - агрегированный результат получают все процессы
  MPI.COMM_WORLD.Allreduce(sendBuf, recvBuf, offset, count, datatype, op)
- Сборка (Gather) - все процессы отправляют данные в root, root собирает их в один массив
  MPI.COMM_WORLD.Gather(sendBuf, sOff, sCount, sType, recvBuf, rOff, rCount, rType, root)
- Обратная сборка (Scatter) - root раздает части своих данных всем процессам
  MPI.COMM_WORLD.Scatter(sendBuf, sOff, sCount, sType, recvBuf, rOff, rCount, rType, root)
- Полная пересылка (Alltoall) - каждый процесс отправляет часть данных каждому процессу
  MPI.COMM_WORLD.Alltoall(sendBuf, sOff, sCount, sType, recvBuf, rOff, rCount, rType)
- Префиксная редукция (Scan) - каждый процесс получает результат по всем процессам с меньшим рангом (включая себя)
  MPI.COMM_WORLD.Scan(sendBuf, recvBuf, offset, count, datatype, op)
- Префиксная редукция без себя (Exscan) - как Scan, но текущий процесс не включается в результат
  MPI.COMM_WORLD.Exscan(sendBuf, recvBuf, offset, count, datatype, op)

#### Класс 4: Управление буфером для буферизованных операций

Методы Bsend и Ibsend требуют предварительного выделения буфера в памяти. Размер буфера должен быть достаточным для хранения всех отправляемых сообщений. Несоблюдение этого требования приведет к ошибке.

- Выделение буфера
  MPI.Buffer_attach(byte[] buffer, int length)
- Отсоединение буфера (освобождение ресурсов)
  MPI.Buffer_detach()
- Операции для редукции (параметр op)
- MPI.SUM - сумма всех элементов
- MPI.PROD - произведение всех элементов
- MPI.MAX - максимальное значение
- MPI.MIN - минимальное значение
- MPI.MAXLOC - максимальное значение и его индекс
- MPI.MINLOC - минимальное значение и его индекс
- MPI.LAND - логическое И (для boolean)
- MPI.LOR - логическое ИЛИ (для boolean)
- MPI.LXOR - логическое XOR (для boolean)
- MPI.BAND - побитовое И (для целых)
- MPI.BOR - побитовое ИЛИ (для целых)
- MPI.BXOR - побитовое XOR (для целых)
- Важные замечания по использованию
- Для отправки одного значения все равно нужно использовать массив (например, new int[]{42})
- Коллективные операции не имеют тегов (tag) в отличие от P2P
- Неблокирующие операции требуют явного вызова Wait или Test, иначе данные могут быть не отправлены
- При использовании Rsend и Irsend получатель должен уже ждать сообщение, иначе поведение не определено
- Буфер для Bsend должен быть достаточно большим, иначе операция завершится ошибкой



 