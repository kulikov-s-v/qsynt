##### Генератор трёхмерных изображений объектов/продукции
# quvisor-synthesizer
###### `версия 0.1`
Используется для исследования возможности обучения нейросетевых моделей на синтетических данных
Генерирует в виртуальной графической среде сцену с трехмерным цифровым двойником продукции, которая может содержать синтезированные дефекты.

___

## Прогресс
[ ] Этап 0.1 Конкретная модель: блистер таблеток
[ ] Этап 0.2 Ограниченный список моделей 
[ ] Этап 1.0 Загрузка любой модели



## Описание API

### Общие соглашения

- Сервис является RESTful и работает по HTTP
- Для обмена используется формат JSON
- Используется авторизация Basic Auth


### Configuration

Конфигурация дефектов и аугментации, используемая для генерации трёхмерных сцен с продукцией и синтеза изображений.

| метод | ресурс |
| ------ | ------ |
| GET | /configurations |
| POST | /configurations |
| PUT | /configurations/:configuration_id |
| GET | /configurations/:configuration_id |
| DELETE | /robots/:configuration_id |

Содержит все необходимые параметры для генерации дефектов
Может дополнительно содержать параметры аугментации трёхмерной сцены и процесса рендеринга
В случае, если параметры аугментации не заданы, используются значения по умолчанию
Конфигурация генератора передаётся в формате JSON в виде объекта `Configuration`, определённого ниже
Ответ на запрос `POST` содержит `configuration_id` добавленного робота.


 __Пример запроса на добавление новой конфигурации__

 ```sh
 curl -X POST http://localhost:8080/robots -H "Content-Type: application/json" -d '<json_configuration>'
```

__Объект Configuration__
```sh
{  
  object: "blister",
  base_plane: "xy",
  defects: {
    common: {
      composition_defects: 1.0,
      painting_defects: 0.0,
      deformation_3d: 0.05,
      deformation_2d: 0.0,  
      scratches: 0.0,  
      inclusions: 0.0
    },
    base_part: {
      lack_defect: 0.0,
      painting_defects: 0.0,
      deformation_3d: 0.0,
      deformation_2d: 0.3,  
      scratches: 0.5,  
      inclusions: 0.5
    }
    tablet_part: {
      lack_defect: 0.5,
      painting_defects: 0.3,
      deformation_3d: 0.3,
      deformation_2d: 0.0,  
      scratches: 0.5,  
      inclusions: 0.5
    }
    capsule_part: {
      lack_defect: 0.0,
      painting_defects: 0.0,
      deformation_3d: 0.5,
      deformation_2d: 0.0,  
      scratches: 0.5,  
      inclusions: 0.5
    }
  }
  augmentation: {
    base_part: {  
      linear_dimensions: [0.025, 0.025, 0.025],
      normal_map_distortion: 0.0,
      material_metallic_distortion: 0.2,
      material_roughness_distortion: 0.0,
      material_specular_distortion: 0.0,
      material_IoR_distortion: 0.0,
    },
    tablet_part: {  
      linear_dimensions: [0.025, 0.025, 0.025],      
      normal_map_distortion: 0.0,
      material_metallic_distortion: 0.0,
      material_roughness_distortion: 0.2,
      material_specular_distortion: 0.0,
      material_IoR_distortion: 0.0,
    },
    capsule_part: {  
      linear_dimensions: [0.025, 0.025, 0.025],
      normal_map_distortion: 0.0,
      material_metallic_distortion: 0.0,
      material_roughness_distortion: 0.0,
      material_specular_distortion: 0.0,
      material_IoR_distortion: 0.2,
    },
  }
}
```


### Blister

Генерация синтетических примеров блистеров с таблетками с конфигурацией дефектов и аугментации, заданной в `configuration`.

| метод | ресурс |
| ------ | ------ |
| POST | /blister/generate |
| POST | /blister/:configuration/generate |

Запрос `POST` запускает генерацию одного примера. 
Генерация может вызываться с помощью метода `POST /blister/:configuration/generate` для генерации с одной из заранее заданных конфигурация 
Запрос обязательно должен содержать параметр sample, принимающий значение `good` для продукции без дефектов и значение `bad` для продукции с дефектами

 __Пример запроса генерации__

 ```sh
 curl -X POST http://158.160.78.128:8080/blister/4/generate -d sample=good
```

Запрос может содержать параметры:  

- `resolution` - целевое разрешение изображения, в пределах [1920 x 1080] 
- `light_sources` - количество источников освещения в интервале от 1 до 10
- `point_light_power` - интенсивность точечных источников освещения от 1 до 300 Вт. 
- `spot_light_power` - интенсивность спотовых источников освещения от 1 до 300 Вт. 
- `surface_light_power` - интенсивность поверхностных источников освещения от 1 до 300 Вт. 
- `sun_light_power` - интенсивность источника освещения "солнце" от 100 до 1000 Вт. 
- `background` - тип заднего плана из списка [`conveyor`, `table`, `random`]
