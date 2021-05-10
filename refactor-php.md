


```php
class Translation
{
    const DETECT_YA_URL = 'https: //translate.yandex.net/api/v1.5/tr.json/detect';
    const TRANSLATE_YA_URL = 'https: //translate.yandex.net/api/v1.5/tr.json/translate';
    
    //сделать свойство приватным
    public $key = "AIza1yCf2zgkmk-nRxdbB4gg49M9GZhmFei55uo";
    
    //сделать метод приватным и статическим(чтобы вызвать из translate_text)
    public function init(){
    // у данного класса нет родительского класса, ошибка
        parent::init();
    // лишние пробелы внутри условия
        if ( empty( $this->key) ) {
        //переменной $key не существует. убрать $
            throw new InvalidConfigException("Field <b>$key</b> is required");
        }
    }
      // типа данных text нет в php, заменить на string
    /** @param $format text format need to translate
     * @return string
     */
     //именование функций не соответсвует PSR, переименовать в translateText
     //$format по-умолчанию заменить на 'plain'
     //не хватает обрабления = пробелами
    public static function translate_text($format="text")
    {
    //повторение кода, вызвать вместо функцию init
        if ( empty( $this->key) ) {
            throw new InvalidConfigException("Field <b>$key</b> is required");
        }

        $values = array(
            'key'    => $this->key,
            //плохая практика считывать get,post - параметры в теле функции, лучше вообще перенести на вход функции
            'text'   => $_GET['text'],
            'lang'   => $_GET['lang'],
            // если заменить дефолтное значение $format на 'plain', то конструкция уйдет
            'format' => $format == "text" ? 'plain' : $format
        );

        $formData = http_build_query($values);

        $ch = curl_init(self::DETECT_YA_URL);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $formData);

        $json = curl_exec($ch);
        curl_close($ch);

        $data = json_decode($json, true);
        //если в data не окажется ключа code, то ошибка
        if($data['code'] == 200)
        {
            return $data['text'];
        }
        //возможно ошибаюсь, но в случае если успешно переведенного текста нет, то вернуть лучше null
        return $data;
    }
}
```

Доработанная версия:
```php
<?php


class Translation
{
    const DETECT_YA_URL = 'https: //translate.yandex.net/api/v1.5/tr.json/detect';
    const TRANSLATE_YA_URL = 'https: //translate.yandex.net/api/v1.5/tr.json/translate';

    private static $defaultKey = "AIza1yCf2zgkmk-nRxdbB4gg49M9GZhmFei55uo";
    
    private static function init($key) {

        $appKey = empty($key) ? self::$defaultKey : $key;

        if ( empty($appKey)) {
            throw new InvalidConfigException("Field <b>key</b> is required");
        }

        return $appKey;
    }

    /**
     * @param $text   string text to translate
     * @param $lang   string lang to translate
     * @param $format string  [optional] format.'plain' by default.
     * @param $key    string  [optional] key. default key will be accepted if
     * @return        string|null return string if success and null on failure to translate.
     */
    public static function translateText($text , $lang, $format = 'plain', $key = '')
    {
        $appKey = self::init($key);

        $params = array(
            'key'    => $appKey,
            'text'   => $text,
            'lang'   => $lang,
            'format' => $format
        );

        $formData = http_build_query($params);

        try {
            $jsonData = self::postData($formData);
        } catch (Exception $e) {
            return null;
        }

        $data = json_decode($jsonData, true);

        return (array_key_exists('code', $data) && $data['code'] == 200) ? $data['text'] : null;

    }

    /**
     * @param $formData
     * @return string
     * @throws Exception
     */
    private static function postData($formData)
    {

        $ch = curl_init(self::DETECT_YA_URL);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $formData);

        $json = curl_exec($ch);
        curl_close($ch);

        if ($json === false) {
            throw new \Exception("Request is failure");
        }

        return $json;
    }
}


```
