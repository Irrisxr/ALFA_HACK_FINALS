# AlfaHack Finals

### Результат:
Место в LB - 1 \
Average Dataset Rating - 3.00

### Команда DRUJBA:
**TeamLead:** Александр Олейников
- Максим Труфанов
- Матвей Савченков
- Екатерина Мустаева
- Амаль Хатуев

**Консультант по вопросам ML & Data Science:** Ксения Кузнецова

### Вычислительные ресурсы
- Процессор: Intel Ice Lake with T4i
- Количество GPU: 1
- Оперативная память: 64 ГБ
- Встроенное хранилище: 200 ГБ SSD
- Количество ядер CPU (vCPU): 16
 Операционная система: Ubuntu

## Задача:
**Кейс:** AutoML
Создайте универсальную модель для бинарной классификации, которая справляется с разными наборами данных и может адаптироваться к изменениям в признаках и распределениях.

#### Данные
9 задач (4 из блока public и 5 из блока private)

Каждый из файлов датасетов содержит следующие признаки:
- ID — уникальный анонимный идентификатор клиента;
- smpl — признак, указывающий на то, к какой выборке относятся данные (train либо test);
- target — целевое значение, которое вы должны спрогнозировать;

## Решение

# J-AMAL
Jazz Automated Machine Analytical Learning

Модель состоит из 4 блоков:

**Preprocessing**:
- Типизация
- Заполнение пропущенных значений
- Отбор признаков через CatBoost feature_selector, использовали grow_policy со значением Lossguide, что позволило сократить время отбора признаков и их качество.
- Нормализация данных

**ModelTraining**

Использовали по две модели CatBoost и XGBoost, т.к. модели градиентных бустингов показывают наилучшую точность среди других моделей при сравнительно быстрой скорости обучения. Также бустинги лучше других моделей справляются с данасетами, в которых присутствует дисбаланс классов. Для моделей использовали warm_start гиперпараметры. Модели обучались с разными значениями depth и максимальное значение depth выбиралось на основе размера конкретного датасета. 

**Ensembling**

Испольовали blending в качестве метода ансамблирования. Веса подбирались автоматически с помощью перебора. 


## Пробовали 
1. Использовали модель градиентного бустинга LightGBM, вес у данной модели практически всегда равнялся 0, в связи с чем исключили модель.
2. Так же использовали blending из двух CatBoost с разным random_state и blending из трёх бустингов(CatBoost, XGBoost, LightGBM) для стекинговой модели с метаоценщиком ввиде логистической регрессии. Отказались от такого вида ансамблирования из-за лимита времени.
3. Использовать байесовскую оптимизацию из skopt для подборав весов в ансамбле. Отказались из-за нехватки времени при большом кол-ве итераций и при малом кол-ве итераций подбирались плохие веса.
4. Пробовали составлять ансамбль из двух моделей CatBoost и XGBoost, каждый из бустингов был обучен на базовом пайплайне, и ещё по одной модели каждого бустинга были обучены на сжатых данных с помощью PCA. Показало неплохой результат, но хуже чем исходный.

![J-AMAL](https://github.com/user-attachments/assets/d3456271-49c5-4a75-ac4e-a81a9ac14e8f)
