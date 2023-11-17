<h3 align="right"> <a href="README-EN.md"> <img src="https://user-images.githubusercontent.com/87089735/213571353-a9f45178-b7e0-41d0-8148-3241ec9d64b2.png" height="20px"> English </a></h3> 

# python-cist

![GitHub-issues](https://img.shields.io/github/issues/WWFyb3NsYXYg/python-cist)
<p align="right">
<img src="https://github.com/WWFyb3NsYXYg/python-cist/assets/87089735/aa557eb3-24d0-44fe-b4ea-307004316d12">
<p>
Клієнт Python для NURE CIST API (https://cist.nure.ua)

## Обмеження на використання функцій не частіше ніж 1 раз у 60 секунд !

## Bстановлення

```
pip install cist
```


# Використання

## Для групи (Розклад для пар лише для груп. Детальніше дивіться в розділі «Пари»)

1) Отримайте свій ідентифікатор групи:
```python
  import cist

  groups = cist.get_groups()
  print(groups)
```
2) Використовуйте цей group_id для ініціалізації клієнта:

```python
  import cist
  group_id = 'xxxxxxxx'

  group = cist.Group(group_id)
  group_schedule = group.get_schedule()
  print(group_schedule)
```
## Для вчителів (Розклад для пар лише для вчителів. Детальніше дивіться в розділі «Пари»)

1) Отримайте ідентифікатор викладача:
```python
  import cist

  struct = cist.get_struct()
  print(struct)
```
2) Використовуйте цей teacher_id для ініціалізації клієнта:

```python
  import cist
  teacher_id = 'xxxxxxxx'

  teacher = cist.Teacher(teacher_id)
  teacher_schedule = teacher.get_schedule()
  print(teacher_schedule)
```
## Для аудиторії (Розклад пар призначений лише для аудиторії. Детальніше дивіться в розділі «Пари»)

1) Отримати ідентифікатор аудиторії:
```python
  import cist

  audiences = cist.get_audiences()
  print(audiences)
```
2) Використовуйте цей audience_id для ініціалізації клієнта:

```python
  import cist
  audience_id = 'xxxxxxxx'

  audience = cist.Auditory(audience_id)
  audience_schedule = grouaudiencep.get_schedule()
  print(audience_schedule)
```

## «Пари»
Розклад заходів (пар), для вчителя, груп чи аудиторій. `Пара – це факт зустрічі в часі та просторі/аудиторії зацікавлених осіб/груп/викладачів з певною метою/предметом.` На одній парі академічна група може бути одночасно кількома дисциплінами (накладками), це прийнято для альтернативних дисциплін. Унікальним є поєднання «час пари + дисципліна».

- `timetable_id` – ідентифікатор групи або викладача
- `type_id`: 1 – група (за замовчуванням), 2 – викладач, 3 – аудиторія
- `time_from` – дата початку вибірки з розкладу (за замовчуванням – початок поточного семестру)
- `time_to` – дата закінчення вибірки з розкладу (за замовчуванням – кінець поточного семестру).

```python
  import cist
  timetable_id = 'xxxxxxxx'
  type_id = 2
  time_from = date(2023,11,17)
  time_to = date(2023,11,18)

  timetable = cist.Couple(timetable_id, type_id)
  event_schedule = timetable.get_schedule(time_from, time_to)
  print(event_schedule)
```
### Результуюче дерево складається з наступних основних частин:
- `time-zone` – назва часового пояса;
- `events` – масив всіх подій (пар) запитаного проміжку часу;
- `subjects` – масив усіх дисциплін, пари яких є у запрошеному проміжку часу;
- `teachers` – масив усіх викладачів, дисципліни яких є у списку;
- `types` – масив усіх видів занять, які є у ВНЗ

```bash
>>> {
    "time-zone":"Europe/Kiev",
    "events":[
        {
           "subject_id":1,
           "start_time":1,
           "end_time":1,
           "type":21,
           "number_pair":1,
           "auditory":"166з",
          "teachers":[
              1,
              2
           ],
           "groups":[
              1,
              2
           ]
        }
    ],
    "groups":[
      {
         "id":1,
         "name":"КН-10-1"
      }
   ],
   "teachers":[
      {
         "id":"1",
         "short_name":"Каук В. И.",
         "full_name":"Каук Виктор Иванович"
      }
   ],
   "subjects":[
      {
         "id":1,
         "brief":"КПО",
         "title":"Конструирование ПО",
         "hours":[
            {
               "type":1,
               "val":20,
               "teachers":[
                  1,
                  2
               ]
            }
         ]
      }
   ],
   "types":[
      {
         "id":21,
         "short_name":"Лб",
         "full_name":"Лабораторна ІОЦ",
         "id_base":20,
         "type":"laboratory"
      }
   ]
}
```

### Подія
- `subject_id` – id дисципліни;
- `start_time` – дата-час початку в секундах (Unix Epoch);
- `end_time` – дата-час початку в секундах (Unix Epoch);
- `type` – id типу пари (детальніше описано нижче);
- `number_pair` – номер пари;
- `auditory` – рядок аудиторії;
- `teachers` – масив викладачів, які мають прийти на пару (1 – на лекцію, 1,2 на лабораторну роботу);
- `groups` – масив груп, які мають прийти на пару;
- `id у group` – це той id, за яким можна отримати розклад групи;
- `id у teacher` – це той id, за яким можна отримати розклад викладача.

### Типи занять
- `0` – лекція (базовий тип, колір жовтий, FEFEEA) – lecture;
- `1` – лекція настановна, перша (цей підтип є у заочників);
- `2` – лекція настановна, що передує семестровому контролю.

- `10` – практичне заняття (базовий тип, зелений DAE9D9) – practice;
- `12` – практичне заняття настановне (цей підтип є у заочників).

- `20` – лабораторна робота (базовий тип, фіолетовий CDCCFF) – laboratory;

- `23` – лабораторна робота на ПЦ настановна;
- `24` – лабораторна робота на кафедрі настановна.

- `30` – консультація (базовий тип, колір білий) – consultation;
- `31` – позанавчальне заняття (необов'язкова консультація. У розкладі це пара у викладача без групи та предмета).

- `40` – залік звичайний (базовий тип, колір коричневий, C2A0B8) – test;
- `41` – залік диференційований (у ХНУРЕ зараз немає, скасували 15 років тому).

- `50` – іспит (базовий тип, у чистому вигляді у ХНУРЕ не використовується, колір темно-блакитний 8FD3FC) – exam;
- `51` – іспит письмовий;
- `52` – іспит усний;
- `53` – іспит комбінований;
- `54` – іспит тестовий;
- `55` – іспит модульний.

- `60` – КП/КР (базовий тип) – course_work.
## Інформація про конкретний об'єкт

Групи одного напряму (опціонально в рамках факультету)

```python
  id_direction = 'xxxxxxxx'
  id_faculty = 'xxxxxxxx'	#не обов'язково

  object = cist.Object(id_direction, id_faculty)

  grp_of_directions = object.get_grp_of_directions()
  print(grp_of_directions)
```

Групи спеціальності (опціонально у рамках факультету)

```python
  id_speciality = 'xxxxxxxx'
  id_faculty = 'xxxxxxxx' 	#не обов'язково

  object = cist.Object(id_speciality, id_faculty)

  grp_of_specialities = object.get_grp_of_specialities()
  print(grp_of_specialities)
```

Всі спеціальності певного напряму, що відносяться до конкретного факультету (актуально для спеціалістів і магістрів)

```python
  id_direction = 'xxxxxxxx'
  id_faculty = 'xxxxxxxx' 

  object = cist.Object(id_direction, id_faculty)

  specialities = object.get_specialities()
  print(specialities)
```

Всі напрямки факультету

```python
  id_faculty = 'xxxxxxxx' 

  object = cist.Object(id_faculty)

  directions = object.get_directions()
  print(directions)
```

Всі викладачі кафедри

```python
  id_department = 'xxxxxxxx' 

  object = cist.Object(id_department)

  teachers = object.get_teachers()
  print(teachers)
```

Всі кафедри факультету

```python
  id_faculty = 'xxxxxxxx' 

  object = cist.Object(id_faculty)

  departments = object.get_departments()
  print(departments)
```


## Методи

### Отримати всю структуру університету з факультетами, напрямів, спеціальностей і груп студентів. Напрямки включають лише групи бакалавр. Для груп спеціалістів і Ступені магістра пов'язані з спеціальності, які включені до направлень

```python
>>> cist.get_groups()

{
   "university":{
      "short_name":"ХНУРЭ",
      "full_name":"Харківський національний університет радіоелектроніки",
      "faculties":[
         {
            "id":1,
            "short_name":"КН",
            "full_name":"Факультет комп`ютерних наук",
            "directions":[
               {
                  "id":1,
                  "short_name":"КН",
                  "full_name":"Комп`ютерні науки",
                  "groups":[
                     {
                        "id":1,
                        "name":"КН-13-1"
                     }
                  ],
                  "specialities":[
                     {
                        "id":1,
                        "short_name":"ІУСТ",
                        "full_name":"Інформаційні управляючі системи і технології",
                        "groups":[
                           {
                              "id":1,
                              "name":"ІУСТ-09-1"
                           }
                        ]
                     }
                  ]
               }
            ]
         }
      ]
   }
}
```
 Напрями містять групи, які не мають спеціальностей. Групи, які мають спеціальність, віднесена до конкретної спеціальності всередині напряму


### Отримати всю структуру університету з факультетами, кафедрами, викладачами.

```python
>>> cist.get_struct()
{
   "university":{
      "short_name":"ХНУРЭ",
      "full_name":"Харківський національний університет радіоелектроніки",
      "faculties":[
         {
            "id":1,
            "short_name":"КН",
            "full_name":"Факультет комп`ютерних наук",
            "departments":[
               {
                  "id":1,
                  "short_name":"ІУС",
                  "full_name":"Кафедра інформаційних управляючих систем",
                  "teachers":[
                     {
                        "id":1,
                        "short_name":"Петров П. П.",
                        "full_name":"Петров Пётр Петрович"
                     }
                  ]
               }
            ]
         }
      ]
   }
}


Властивості: departments – масив кафедр факультету.


```
### Отримайте всю структуру університету з корпусами, аудиторіями та видами аудиторій. Аудиторії розташовані у корпусах. Кожна аудиторія може відноситися до нуля, одному або декільком типам

```python
>>> cist.get_audiences()

{
   "university":{
      "short_name":"ХНУРЕ",
      "full_name":"Харківський Національний Університет Радіоелектроніки",
      "buildings":[
         {
            "id":"а",
            "short_name":"а",
            "full_name":"корпус \"а\"",
            "auditories":[
               {
                  "id":"3931027",
                  "short_name":"285",
                  "floor":"2",
                  "is_have_power":"1",
                  "auditory_types":[
                     {
                        "id":"1684555",
                        "short_name":"ПІ"
                     }
                  ]
               },
               {
                  "id":"97",
                  "short_name":"287",
                  "floor":"2",
                  "is_have_power":"1",
                  "auditory_types":[
                     {
                        "id":"20",
                        "short_name":"Каф_ВЦ"
                     },
                     {
                        "id":"1684555",
                        "short_name":"ПІ"
                     }
                  ]
               }
            ]
         }
      ]
   }
}
```
#### Властивості:
- is_have_power – ознака наявності розетки в аудиторії;
- floor – поверх, де розташована аудиторія;
- types – масив типів, яких належить аудиторія. Одна аудиторія може ставитись до
кільком типам. Деякі типи характеризують аудиторію як таку (великі лекційні аудиторії, малі лекційні аудиторії, аудиторії для групових занять, аудиторії обчислювального центру тощо), так і належність аудиторії до певного підрозділу ВНЗ (аудиторії кафедри ІВС, аудиторії кафедри ПІ, аудиторії центру технологій дистанційної освіти тощо).


### Отримати всі факультети університету

```python
>>> cist.get_faculties()

```

### Отримайте всі типи університетських аудиторій

```python
>>> cist.get_audiences_types()

```




### Ви також можете передати об’єкти datetime ``get_schedule(time_from, time_to)``
```python
>>> group.get_schedule(date(2023,11,17), date(2023,11,17))
```

