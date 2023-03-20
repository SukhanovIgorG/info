# Основы работы с Mongo DB

- Mongo DB noSQL база данных
- Докуметоориентированная база данных.
- Документы хранятся в JSON подобном формате
- Не требует описания схем таблиц, как в реляционных базах
- У документов в одной коллекции не обязательна строгая структура, какие то поля могут отсутствовать а какие то быть дополнительно
- Данные хранятся в виде коллекций и документов внутри коллекций

  - DataBase

    - collection 1
    - collection 2
      - document 1 { key: value }
      - document 2 { key: { key: value } }....

    <details>
    <summary>Пример</summary>
    <br>

        {
          _id: ObjectId("64115c40a3e55526d83cea33"),
          center: [
            0,
            0
          ],
          name: '123',
          description: 'тест',
          floor: '1',
           place: ObjectId("60f971e50d4a8a0029afd46f"),
          createdAt: 2023-03-15T05:48:48.286Z,
          updatedAt: 2023-03-17T06:18:31.136Z,
          __v: 0,
          areas: {
            'ree4f7de3-1234-43e0-8057-b3704d1556d2': {
              stroke: null,
              'stroke-width': 1,
              'stroke-dasharray': '1 5',
              fill: 'rgba(205,24,214,0.5)',
              'ref4873fe-e1d5-4a31-9cc4-5ec55fd9b38d': {
                cx: 670,
                cy: 220,
                id: 'point_area',
                data_object: 'ref4873fe-e1d5-4a31-9cc4-5ec55fd9b38d',
                data_type: 'M',
                data_vector: 'none'
              },
              type: null,
            }
          }
        }

    </br>
    </details>

  Инструменты:

  - [Mongo DB. download community server](https://www.mongodb.com/try/download/community) <= (для начала загружаем сервер)
  - [Mongo DB. download Compass](https://www.mongodb.com/try/download/compass) так же будет полезен графический интерфейс для работы с данными

## Начало работы:

## Команды Mongoose BASH:

### Справка

    help

### Просмотр всех баз данных

    show databasses

### Выбрать базу

для дальнейшей работы. пример development
( Если базы с таким именем не существовало, она будет создана )

    use developmant

### Создание коллекций.

например пользователей

    db.createCollections("users")

### Просмотр существующих коллекций

    show collections

### Удаление базы данных

    db.dropDatabase()

### Добавление документов в коллекцию insertOne() или insertMany()

    db.users.insertOne({
      name: 'John',
      age: 25,
    })


    db.users.insertMany(
      [
        { name: 'John', age: 25},
        { name: 'Mick', age: 15},
        { name: 'lora', age: 56},
      ]
    )

### Поиск документов find() findOne()

    db.users.find() // найдет всех

    db.users.find({ name: 'Mick'}) // найдет всех с именем 'Mick'

    db.users.find(
      $or: [
        { name: 'Mick'},
        {age: 25}
      ]
    )
    // найдет всех илис  именем 'Mick' или возрастом 25

    db.users.find(
      { age:
        {$gt: 20}
      }
    ) // найдет всех старше 20

### Критерии поиска:

    $lt  // (less than) - меньше чем
    $lte // (less than or equal) - меньше чем или равно
    $gt  // (greater than) - больше чем
    $gte // (greater than or equal) - больше чем или равно
    $ne  // (not equal) - не равно

### Поиск по наличию поля

значением поля передается объект с ключом $exists: true

    db.users.find(
      {
        posts: {
          $exists: true
        }
      }
    )
    // Получим пользователей у которых есть посты

### Параметры выдачи

Вторым параметром принимается объект

    db.users.findOne(
      { name: 'Mick'},
      { posts: 1 }
    )
    // Получим в выдаче только посты без остальных полей

### Поиск внутри массива

Вторым параметром принимается объект $elemMatch

    db.users.find(
      { posts: {
        $elemMatch: {
          title: 'some title',
        }
      }
    )
    // Получим пользователей у которых есть посты с заголовком 'some title'

### Сортировка sort()

После функции find вызывается sort с параметрами с параметром, по которому требуется сортировка и значением 1 или -1 (возрастание или убывание)

    db.users.find().sort({age: -1}) // все пользователи по убыванию возраста

### Ограничение количества данных в ответе limit()

    db.users.find().sort({age: -1}).limit(1) // получим один документ

Поиск по \_id

При создании документа, база присваивает уникальный номер \_id, который имеет тип ObjectId('64145518da42fc0c445a7194')

    db.users.findOne(
      _id: ObjectId('64145518da42fc0c445a7194')
    ) // найдет конкретного пользователя

### Действия после поиска

После поиска можно сразу что то сделать с документом, в зависимости от цели, существуют методы

    findOneAndUpdate // найти одного и обновить
    findOneAndDelete // найти одного и удалить
    findOneAndReplace // найти одного и заменить

### Обновить update(), updateOne или updateMany

при изменении, первый объект параметры поиска, второй содержит ключ $set: и заменяемые поля и их значения внутри объекта

    db.users.update(
      {
        age: {
          $gt: 30
        }
      },
      {
        $set: {
          name: 'Дед'
        }
      }
    )

// Всем пользователям старше 30 присваиваем имя Дед

// рекомендуется использовать методы updateOne или updateMany

### Переименование полей rename

если заменить $set на $rename а значение на новое имя, сможет переименовать поле

      db.users.update(
      {
        age: {
          $gt: 30
        }
      },
      {
        $rename: {
          name: 'fullname'
        }
      }
    )
    // Поле name для всех юзеров старше 30 изменено на fullname

### Удаление. deleteOne или deleteMany

    db.users.deleteOne(
      _id: ObjectId('64145518da42fc0c445a7194')
    )
    //Будет удален пользователь с id '64145518da42fc0c445a7194'

### Объединение действий в одном запросе bulkWrite().

Например добавить одного пользователя и удалить другого. В Качестве аргументов принимает МАССИВ! с объектами, ключи в объекте - методы. filter - принимает значения для фильтрации

    db.users.bulkWrite([
      {
        insertOne: {
          name: 'Oleg',
          age: 25
        }
      },
      {
        deleteOne: {
          filter: {
            name: 'Mikh'
          }
        }
      }
    ])

## Сяязи

- Один ко многим / многие к одному
  (Пользователь может иметь много постов, в то время как пост имеет лиш одного автора)

        db.users.update(
        {
          name: oleg
        },
        {
          $set: {
            posts: [
              { title: 'post one', text: 'lorem ipsum one' },
              { title: 'post two', text: 'lorem ipsum two' },
              { title: 'post tree', text: 'lorem ipsum tree' },
            ]
          }
        }
        )
        // у пользователя Олега появится массив с постами

Лучшим подходом, будет создание отдельной коллекции постов, а в массив постов пользователя сложить id этих постов. Это улучшит структуру

## Работа из JS:

### Инициализация

### Схемы

### Контроллеры
