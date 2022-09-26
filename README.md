# API-sheme-for-bikeShop

## Пользователь
Создание пользователя
Для создания пользователя делать POST запрос в ручку /users со следующей структурой:
```Java Script
{
    "username": "String",
    "password": "String"
}
```

Ответом ручки является следующая структура User:
```
{
    "id": "String",
    "username": "String",
    "description": "String",
    "avatar": [File],
    "createAt": "String",
    "updateAt": "String"
}
```
## Авторизация
Авторизация реализована через Basic access authentication в ручке /auth. Возвращается токен в виде строки.

Настройки пользователя:
Для изменения настроек аккаунта пользователя ручка PUT /settings
Используется Bearer авторизация с полученным ранее токеном.
```
{
    "username": "String",
    "description": "String",
    "avatar": [File],
}
```
## Объявления
Для получения списка объявлений используется ручка GET /advertisements.
Доступна фильтрация через параметры запроса:
по месту - location=locationId
по категории - category=categoryId
(от фронта: в фильтре есть priceRange и состояние condition)
Ответ возвращается в следующем виде:
```
{
    "items": [
        {
            "id": "UUID",
            "title": "String",
            "description": "String",
            "createAt": "Date",
            "updateAt": "Date",
            "price": 15,
            "published": true,
            "categories": [
                {
                    "id": "UUID",
                    "name": "Имя категории",
                }
            ],
            "location": {
                "id": "UUID",
                "name": "String",
                "parentId": "UUID?"
            },
            "photos": ["String"]
        }
    ]
}
```
+ Пагинация

## Создание объявления
Для создания объявления используется ручка POST /advertisements. Используется Bearer авторизация с полученным ранее токеном. Ручка принимает объявление в виде multipart form:

struct AdvertisementInsertRequest: 
```
Content {
  let title: String
  let description: String
  let date: String
  let price: Double
  let categoryId: UUID
  let locationId: UUID
  let photos: [File]
}
```
cURL запроса:
curl --location --request POST 'localhost:8080/advertisements/' \
--form 'title="велек 3"' \
--form 'description="10/10 крутой"' \
--form 'price="8800"' \
--form 'photos[0]=@"/Users/mikita/Desktop/IMG_6351.jpeg"' \
--form 'category_id="1243fc56-4e6e-4dd2-aac3-497dc6900cad"'


## Работа с категориями
Получение категорий
Для получения списка категорий используется ручка GET /categories. Возвращаются сразу все возможные категории.
```
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

