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

## Команды BASH:

## Работа из JS:
