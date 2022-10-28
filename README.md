# API-sheme-for-bikeShop

## Пользователь

Для создания пользователя делать POST запрос в ручку /users со следующей структурой:
```JSON
{
    "username": "String",
    "password": "String"
}
```

Ответом ручки является следующая структура User:

```JSON
{
    "id": "String",
    "username": "String",
    "description": "String",
    "avatar": [File],
    "location": "locationId",
    "createAt": "String",
    "updateAt": "String"
}
```
## Авторизация
Авторизация реализована через Basic access authentication в ручке /auth. Возвращается токен в виде строки.

## Интерфейс пользователя
#### Настройки пользователя:
Для изменения настроек аккаунта пользователя ручка PUT /settings
Используется Bearer авторизация с полученным ранее токеном.

```JSON
{
    "username": "String",
    "description": "String",
    "location": "locationId",
    "avatar": [File],
}
```

#### Раздел мои объявления  
Раздел имеет вкладки: активные, на модерации, отказ, деакивированные
Ручка GET /myAdvertisements с авторизацией по токену 
Структура ответа:

```JSON
{
"active": [{}, {}],
"moderation": [{}, {}],
"rejected": [{}, {}],
"deactivated": [{}, {}]
}
```
Либо можно сделать на каждую вкладку по ручке

## Объявления
#### Фильтрация
Ручка GET /advertisements
Доступна фильтрация через параметры запроса:
по месту - location=locationId
по категории - category=categoryId

(Так же в фильтре есть ценовой диапазон - priceRange и состояние - condition которые тоже можно добавить в фильтры запроса)

#### Фильтрация 
Включает в себя пагинацию
Ручка GET /api/item/getall?typeid=1&locationid=1&limit=10&page=1&price=0,200&state=1

Ответ возвращается в следующем виде:

```JSON
[
    {
        "id": 3,
        "username": "\"petya\"",
        "title": "\"title\"",
        "state": 1,
        "userid": 2,
        "description": "\"lorem\"",
        "price": 200,
        "published": true,
        "locationid": 1,
        "typeid": 1,
        "photo": [
            "16393a67-ca77-4a54-8870-c624d0557522.jpg",
            "277cea8d-7000-4d80-bb7e-15a2a167f7f4.jpg"
        ],
        "createdAt": "2022-10-27T09:51:58.687Z",
        "updatedAt": "2022-10-27T09:51:58.687Z"
    }
]
```

## Создание объявления
Для создания объявления используется ручка POST /advertisements.
Используется Bearer авторизация с полученным ранее токеном. Ручка принимает объявление в виде multipart form:

struct AdvertisementInsertRequest: 
```
  {
    title: String
    description: String
    date: String
    price: Double
    categoryId: UUID
    locationId: UUID
    photos: [File]
}
```

cURL запроса:
curl --location --request POST 'localhost:8080/advertisements/' \
--form 'title="велек 3"' \
--form 'description="10/10 крутой"' \
--form 'price="8800"' \
--form 'photos[0]=@"/Users/mikita/Desktop/IMG_6351.jpeg"' \
--form 'category_id="1243fc56-4e6e-4dd2-aac3-497dc6900cad"'
## Update объявления 
Ручка  patch http://localhost:5000/api/item/update

```
  {
    title: String
    description: String
    price: Double
    typeid: UUID
    locationId: UUID
    img: [File]
    state: Number
    id: Number //Обязательный параметр
}
```

## Поиск 

Поиск в рамках категории, по тайтлу объявления и локации.
Параметры запроса:
по месту - location=locationId
по категории - category=categoryId
по имени - title=searchText

Ручка GET /search
  
## Работа с категориями
Получение категорий
Для получения списка категорий используется ручка GET /categories. Возвращаются сразу все возможные категории.
```JSON
    {
    "categories": [
        {
            "id": "id1",
            "name": "Родительская категория 1"
        },
        {
            "id": "id2",
            "name": "Родительская категория 2",
        },
        {
            "id": "id3",
            "name": "Дочерняя категория 1",
            "parentId": "id1"
        },
        {
            "id": "id4",
            "name": "Дочерняя категория 2",
            "parentId": "id1"
        }
    ]
} 
```
На стороне фронта следует отображать только непосредственно дочерние категории для какой-то корневой. То есть при parentId равном None отображать категории с parentId == None. При parentId равном "id1" отображать категории с parentId == "id1".

