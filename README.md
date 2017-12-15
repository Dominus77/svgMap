![SvgMap](https://preview.ibb.co/jMWOnw/Screenshot_20171207_161726.png "Yii2 widget for svg-map construction")


# SvgMap

Yii2-виджет для генерации svg по заданному источнику данных.

## Установка

Выполняем команду
```
php composer.phar require hopper/svg-map "*"
```
или добавляем в composer.json
```json
{
    "require": {
        "ghopper/svg-map": "*"
    }
}
```

## Пример использования

### Подключаем пространство имен
```php
use ghopper\svgmap\SvgMapWidget;
```

### В контроллере получаем данные для построения svg (в данном случае из примера) и передаем в шаблон
```php
$file = Yii::getAlias('@ghopper/svgmap/example') . "/russia.json";
$data = file_get_contents($file);
$states = json_decode($data);
...
$this->render('index', ['svgData' => $states]);
```
### В шаблоне создаем js-обрыботчики событий
```javascript
<?php
$this->registerJs(new yii\web\JsExpression("
    var customClick = function (path) {
        alert(path.attr('id') + ' - ' + path.attr('title'))
    };
"), \yii\web\View::POS_READY);
?>
```
### И там же передаем все данные виджету
```php
<?php
    echo SvgMapWidget::widget([
        'type' => SvgMapWidget::DATA_SOURCE_ARRAY,
        'data' => $svgData,
        'onClick' => 'customClick',
    ]);
?>
```

## Источники данных
Вы можете добавлять свои данные к базовому формату:

```
{
    title: "Элемент #1",
    d: ["M 50,50 10,0 0,10 -10,0 z"]
}

```
Например добавить id, кол-во элементов, адрес перехода или другие данные,которые вам могут понадобиться. В последующем вы можете манипулировать этими данными в обработчиках событий.

Виджет поддерживает два источника данных:
 * SvgMapWidget::DATA_SOURCE_ARRAY - php-массив
 * SvgMapWidget::DATA_SOURCE_JSON_URL - ссылка на json

## Внешний вид
```
<?= SvgMapWidget::widget([
    //...
    // Html опции контейнера svg_map
    'containerOptions' => [
        'class' => 'svg_map',                    
    ],
    // Html опции для контейнера toolTip
    'toolTipContainerOptions' => [
        'class' => 'tooltip',
    ],
    // Опции для toolTip, коррекция позиционирования
    'toolTipOptions' => [
        'position' => [
            'x' => 80,
            'y' => 120,
        ]
    ],
    // Html заголовки и опции для тега svg
    'svgContainerOptions' => [],
]); ?>
```
Можете создавать сложную анимацию в обработчиках событий.

## Параметры запуска виджета
### Обязательные
 * type - тип источника данных
 * data - данные к параметру type (адрес json либо массив)

### Дополнительные
 * showTip - флаг, определяющий отображать или нет toolTip
 * onClick - callback-функция клика по элементу svg path
 * onOver - callback-функция, вызываемая при движении курсора над элементом
 * onOut - callback-функция, вызываемая когда курсор покидает элемент
