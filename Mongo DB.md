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

  <details>
    <summary>Конфиг .env</summary>
    <br>

    # MONGODB TESTSERVER

    MONGODB_USER_DEV= "root"
    MONGODB_PASSWORD_DEV= "example"
    MONGODB_DBNAME_DEV= "development"
    MONGODB_PROTOCOL_DEV= "mongodb:"
    MONGODB_HOST_DEV= "94.26.226.9:27017"
    MONGODB_QUERY_DEV= "authSource=admin&readPreference=primary&appname=MongoDB%20Compass&ssl=false"

  </details>

  <details>
    <summary>Конфиг ДБ mongoDBConfig.js</summary>
    <br>

      const common = {
          dbNameStatistics: process.env.MONGODB_STATISTICS_COLLECTION,
          dbNameMonitoring: process.env.MONGODB_MONITORING_COLLECTION
      };

      const dev = {
          dbUser: process.env.MONGODB_USER_DEV,
          dbPassword: process.env.MONGODB_PASSWORD_DEV,
          dbName: process.env.MONGODB_DBNAME_DEV,
          dbProtocol: process.env.MONGODB_PROTOCOL_DEV,
          dbHost: process.env.MONGODB_HOST_DEV,
          dbQuery: process.env.MONGODB_QUERY_DEV,
          dbNameStatistics: common.dbNameStatistics,
          dbNameMonitoring: common.dbNameMonitoring
      };

      const prod = {
          dbUser: process.env.MONGODB_USER,
          dbPassword: process.env.MONGODB_PASSWORD,
          dbName: process.env.MONGODB_DBNAME,
          dbProtocol: process.env.MONGODB_PROTOCOL,
          dbHost: process.env.MONGODB_HOST,
          dbQuery: process.env.MONGODB_QUERY,
          dbNameStatistics: common.dbNameStatistics,
          dbNameMonitoring: common.dbNameMonitoring
      };

      module.exports = {
          dev,
          prod
      };

  </details>

  <details>
    <summary>Приложение app.js</summary>
    <br>

      const express = require("express");
      const mongoose = require("mongoose");
      const expressSession = require("express-session");
      const MongoStore = require("connect-mongo")(expressSession);
      const Connector = require("./backend/helpers/db/Connector.js");
      const nadModels = require("./backend/models/nad.js");
      const router = require("./backend/router");
      const connector = new Connector();

      global.nad = {
        models: {},
      };

      const appInit = () =>
        new Promise((resolve, reject) => {
          preloader([connector.connect()])
            .then((message) => {
              nadModels.setModels(connector.connection);
              const app = express();

              app.use(
                expressSession({
                  secret: "neuro AD my super key",
                  resave: false,
                  saveUninitialized: false,
                  store: new MongoStore({ mongooseConnection: connector.connection }),
                })
              );

              router(app);

              resolve(app);
            })
            .catch((error) => {
              reject(error);
            });
        });

      module.exports = appInit;

  </details>

  <details>
    <summary>Коннектор Connector.js</summary>
    <br>

    const mongoose = require("mongoose");
    const mongoDBConfig = require("../../configs/mongoDBConfig.js");

    const Connector = class {
        constructor(options = {}) {
            this.isDev = process.env.NODE_ENV === "development";
            this.connection = null;

            this.connect = this.connect.bind(this);

            this.mergeOptions(options);
        }

        mergeOptions(options) {
            const { dbUser, dbPassword, dbName, dbProtocol, dbHost, dbQuery } = this.isDev
                ? mongoDBConfig.dev
                : mongoDBConfig.prod;

            this.dbUser = options.dbUser || dbUser;
            this.dbPassword = options.dbPassword || dbPassword;
            this.dbName = options.dbName || dbName;
            this.dbProtocol = options.dbProtocol || dbProtocol;
            this.dbHost = options.dbHost || dbHost;
            this.dbQuery = options.dbQuery || dbQuery;
        }

        connect() {
            return new Promise((resolve, reject) => {
                const { dbUser, dbPassword, dbName, dbProtocol, dbHost, dbQuery } = this;
                const mongoDBHref = `${dbProtocol}//${dbUser}:${dbPassword}@${dbHost}/${dbName}?${dbQuery}`;
                const hiddenPassword = dbPassword ? "***" : "";
                const noPassHref = `${dbProtocol}//${dbUser}:${hiddenPassword}@${dbHost}/${dbName}?${dbQuery}`;
                const options = {
                    useNewUrlParser: true,
                    useCreateIndex: true,
                    useFindAndModify: false,
                    useUnifiedTopology: true,
                    promiseLibrary: global.Promise,
                    poolSize: 5,
                    socketTimeoutMS: 30000,
                    family: 4,
                    // authSource: dbName
                };

                this.connection = mongoose.createConnection(mongoDBHref, options);

                this.connection
                    .once("open", () => {
                        console.log("Open connect to mongo server:", noPassHref);

                        resolve();
                    })
                    .on("connected", () => {
                        console.log("Connected to mongo server:", noPassHref);
                    })
                    .on("disconnected", () => {
                        console.log("Disconnected from mongo server:", noPassHref);
                    })
                    .on("error", () => {
                        console.error(`MongoDB error for connection: ${noPassHref}`);

                        reject();
                    });
            });
        }
    };

    module.exports = Connector;

  </details>

  <details>
    <summary>Модель nad.js</summary>
    <br>

    const CategorySchema = require("../modelSchemas/guide/category.js");
    const UserSchema = require("../modelSchemas/users/userWithSectionsAccess.js");
    const createModels = require("./createModels.js");

    const createModels = function (connection, schemas, models = {}) {
      if (!connection) {
          return console.error(`${this.name}() "connection" parameter is missing.`)
      }

      if (!schemas || typeof schemas !== 'object') {
          return console.error(`${this.name}() "schemas" parameter is missing or not correct.`);
      }

      const result = models;

      Object.keys(schemas).forEach((key) => {
          const modelName = key;
          const schema = schemas[key];

          if (!result[modelName]) {
              result[modelName] = connection.model(modelName, schema);
          }
      });

      return result;
    };

    const NadModels = class {
      constructor() {
        if (global.nad.models instanceof NadModels) {
          return global.nad.models;
        }

        global.nad.models = this;

        this.connection = null;
        this.models = {
          Category: null,
          User: null,
        };
      }

      setModels(connection) {
        if (!this.connection && connection) {
          this.connection = connection;
        }
        const schemas = {
          Category: CategorySchema,
          User: UserSchema,
        };

        createModels(this.connection, schemas, this.models);
      }

      get User() {
        return this.models.User;
      }
    };

    module.exports = new NadModels();

  </details>
  <details>
    <summary>Схема UserShema.js</summary>

    const { Schema } = require("mongoose");
    const passportLocalMongoose = require("passport-local-mongoose");

    const UserWithSectionsAccessSchema = new Schema(
        {
            godMode: {
                type: Boolean,
                required: false,
                default: false,
            },
            username: {
                type: String,
                unique: true,
                required: true,
            },
            name: {
                type: String,
                required: true,
            },
            email: {
                type: String,
                required: true,
                validator: function (v) {
                    return /\S+@\S+\.\S+/.test(v);
                },
                message: (props) => `${props.value} is not a valid email!`,
            },
            password: String,
            sectionsAccess: {
                type: Object,
                default: {},
            },
            lock: {
                type: Boolean,
                required: false,
                default: false, //это пока. Возможность блокировки пользователя.
            },
            alarm: {
                type: Boolean,
                required: false,
                default: false,
            },
            photo: {
                type: String,
                required: false,
            },
            referer: {
                type: Schema.Types.ObjectId,
                ref: "User",
                required: true,
            },
            registrationIp: {
                type: String,
                required: true,
            },
        },
        { timestamps: true, minimize: false }
    );

    UserWithSectionsAccessSchema.virtual("user_places", {
        ref: "UserPlace",
        localField: "_id",
        foreignField: "user",
    });

    UserWithSectionsAccessSchema.plugin(passportLocalMongoose);

    module.exports = UserWithSectionsAccessSchema;

  </details>

### Схемы

### Контроллеры

#### Поиск, операции, сохранение

Задача: Найти все карты, в которых использовалась area с типом "some-type" и заменить там значения fill, stroke, type

      // функция производящая необходимые изменения
      // в данном случае ищет поле по заданному значению targetValue
      // и заменяет внутри объекта значения заданных полей на новые переданные ей

      function replaceObjectValue(
        obj,
        targetValue,
        newValue1,
        newValue2,
        newValue3
      ) {
        for (var key in obj) {
          if (typeof obj[key] === "object") {
            replaceObjectValue(
              obj[key],
              targetValue,
              newValue1,
              newValue2,
              newValue3
            );
          } else if (obj[key] === targetValue) {
            obj.fill = newValue1;
            obj.stroke = newValue2;
            obj.type = newValue3;
          }
        }
      }

      // ищем все документы по заданным параметрам
      const updateAreasInMaps = await nadModels.Maps.find({
        place: req.currentPlace._id,
        areas: {
          $exists: true,
        },
      });

      // ко всем найденным документам применяем функцию изменяющую данные
      for (let i = 0; i < updateAreasInMaps.length; i++) {
        const map = updateAreasInMaps[i];
        replaceObjectValue(
          map.areas,
          "some-type",
          fill,
          stroke,
          newType
        );

      // !! если были изменены поля внутри объекта, нужно явно сообщить об этом !!
      map.markModified("areas");

      // После всех необходимых операций над объектами производится сохранение
      await map.save();
    }
