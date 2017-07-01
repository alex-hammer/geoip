# Модуль для Битрикс «GeoIp Api»

[Api](#api)

[Пример использования](#Пример-использования)

[Требования](#Требования)

[Контакты](#Контакты)

## Описание
Модуль предоставляет api для определения местоположения по ip-адресу. Если ip-адрес не указан явно, то местоположение определяется по текущему ip пользователя.

В местоположение входят:

* город;
* код страны;
* название страны на языке сайта;
* код страны в CMS 1С Битрикс
* регион;
* район;
* ширина и долгота;
* диапазон ip-адресов, в который входит переданный.

Местоположение определяется по 2м службам: ipgeobase.ru и freegeoip.net. В случае необходимости, данные из первой уточняются данными из второй.

Для уменьшения количества запросов, полученная информация сохраняется в куках.

Модуль доступен на [Маркетплейсе Битрикса](http://marketplace.1c-bitrix.ru/solutions/rover.geoip/).

## Api
### `\Rover\GeoIp\Location`
#### `public static \Rover\GeoIp\Location::getInstance($ip = null, $charset = self::CHARSET__UTF_8)`
Метод возвращает объект `\Rover\GeoIp\Location` для переднного ip-адреса. Если ip-адрес не передан, то объект возвращается для текуего ip пользователя.

Вторым параметром можно передать кодировку возвращаемых данных. Если она не передана, то по умолчанию берется utf-8.
#### `public reload($ip = null)`
Метод позволяет загрузить данные напрямую из сервисов геопозиционирования, минуя кеш.

Если параметр `$ip` не указан, то местоположение будет загружено для текущего ip-адреса.

Так же, благодаря этому методу, можно несколько раз использовать объект `\Rover\GeoIp\Location` для определения местоположения по разным ip-адресам, а не создавать каждый раз новый (см. [пример использования](#Пример-использования)).
#### `public static getCurIp()`
Возвращает ip-адрес текущего пользователя.
#### `public getData($field = null)`
Возвращает ассоциативный массив со всеми данными, которые удалось получить по ip. Если данные получить не удалось, в массиве возвращается только элемент `ip`.

Есть возможность получить значение только одного поля. Для этого надо передать его имя в параметре.

	[
		'ip'            => 'xxx.xxx.xxx.xxx',
		'inetnum'       => '...',
		'country'       => '...',
		'country_name   => '...',
		'country_id     => '...',
		'city'          => '...',
		'region'        => '...',
		'district'      => '...',
		'lat'           => '...',
		'lng'           => '...'
	]	
	
#### `public getCity()`
Возвращает название города.	
#### `public getCountry()`
Возвращает буквенный код страны.	
#### `public getCountryName()`
Возвращает название страны на текущем языке сайта.
#### `public getCountryId()`
Возвращает id страны в Битриксе.
#### `public getRegion()`
Возвращает название региона.	
#### `public getDistrict()`
Возвращает название района.
#### `public getLat()`
Возвращает широту.	
#### `public getLng()`
Возвращает долготу.					
#### `public getInetnum()`
Возвращает диапазон адресов, в который входит переданный ip.	
## Пример использования
### для сайта в кодировке utf-8

	use Bitrix\Main\Loader,
        Rover\GeoIp\Location;

    if (Loader::includeModule('rover.geoip')){
        try{
            echo 'ваш ip: ' . Location::getCurIp() . '<br><br>'; // текущий ip
            
            $location = Location::getInstance('5.255.255.88'); // yandex.ru
            
            echo 'ip: '                 . $location->getIp() . '<br>';          // 5.255.255.88
            echo 'город: '              . $location->getCity() . '<br>';        // Москва
            echo 'код страны: '         . $location->getCountry() . '<br>';     // RU
            echo 'название страны: '    . $location->getCountryName() . '<br>'; // Россия
            echo 'код страны в Битриксе: '    . $location->getCountryId() . '<br>'; // 1
            echo 'регион: '             . $location->getRegion() . '<br>';      // Москва
            echo 'округ: '              . $location->getDistrict() . '<br>';    // Центральный федеральный округ
            echo 'широта: '             . $location->getLat() . '<br>';         // 55.755787
            echo 'долгота: '            . $location->getLng() . '<br>';         // 37.617634
            echo 'диапазон адресов: '   . $location->getInetnum() . '<br><br>';     // 5.255.252.0 - 5.255.255.255
    
            $location->reload('173.194.222.94'); // google.ru
    
            echo 'ip: '                 . $location->getIp() . '<br>';          // 173.194.222.94
            echo 'город: '              . $location->getCity() . '<br>';        // Mountain View
            echo 'код страны: '         . $location->getCountry() . '<br>';     // US
            echo 'название страны: '    . $location->getCountryName() . '<br>'; // США
            echo 'код страны в Битриксе: '    . $location->getCountryId() . '<br>'; // 122
            echo 'регион: '             . $location->getRegion() . '<br>';      // California
            echo 'округ: '              . $location->getDistrict() . '<br>';    //
            echo 'широта: '             . $location->getLat() . '<br>';         // 37.4192
            echo 'долгота: '            . $location->getLng() . '<br>';         // -122.0574
            echo 'диапазон адресов: '   . $location->getInetnum() . '<br>';     //
            
        } catch (\Exception $e) {
            echo $e->getMessage();
        }
	} else 
        echo 'Модуль GeoIp Api не установлен';
### для сайта в кодировке windows-1251

    use Bitrix\Main\Loader,
        Rover\GeoIp\Location,
        Rover\GeoIp\Service\Base;
	
    if (Loader::includeModule('rover.geoip')){
        try{
            echo 'ваш ip: ' . Location::getCurIp() . '<br><br>'; // текущий ip
            
            $location = Location::getInstance('5.255.255.88', Base::CHARSET__WINDOWS_1251); // yandex.ru
            
            -//-
		
## Компоненты
### Указатель местоположения пользователей (geoip.user.location)
Позволяет установить местоположение для пользователей на основе данных из модуля. Местоположение определяется по ip-адресу, с которого они впервые зашли на сайт. 

Для корректной работы компонента необходимо, чтобы на сайте велся лог сессий.

Определившиеся значения отображаются в зеленой рамочке. Чтобы обновить значение, необходимо выделить галочкой соответствующую строку и нажать «Обновить».

В визуальном редакторе компонент находится по адресу `Компоненты Rover -> Служебные -> Указатель местоположения пользователей`.
#### Параметры
* PAGE_SIZE - Количество пользователей на одной странице
* CITY_FIELDS - Поля пользователя, куда следует внести информацию о городе
* STATE_FIELDS - Поля пользователя, куда следует внести информацию о регионе
* COUNTRY_FIELDS - Поля пользователя, куда следует внести информацию о стране
## Требования	
Для работы «GeoIp Api» необходим установленный на хостинге php версии 5.4 или выше и модуль CURL.
## Контакты
По всем вопросам вы можете связаться со мной по email: rover.webdev@gmail.com, либо через форму на сайте http://rover-it.me.

## Пожертвования
Если решение оказалось вам полезным, вы можете сделать пожертование

<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
<input type="hidden" name="cmd" value="_s-xclick">
<input type="hidden" name="hosted_button_id" value="9ATKYN9FL695L">
<input type="image" src="https://www.paypalobjects.com/ru_RU/RU/i/btn/btn_donateCC_LG.gif" border="0" name="submit" alt="PayPal — более безопасный и легкий способ оплаты через Интернет!">
<img alt="" border="0" src="https://www.paypalobjects.com/ru_RU/i/scr/pixel.gif" width="1" height="1">
</form>
