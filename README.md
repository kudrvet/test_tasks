# test_tasks

# 1
Нужно написать на php функцию, которая принимает строку — текст на любом языке и возвращает массив из 5 наиболее часто встречающихся слов в этом тексте. Ключ массива — слово, значение — количество. Ни веб-сервер, ни база данных не понадобятся; версия php не имеет значения.

**Решение:**
```php
function getPopularWords($text)
{
    $punctuationMarks = ['?','!',',','.',';',':'];
    $words = explode(' ', trim($text));
    $filtered = array_filter($words);
    $preparedWords = array_map(function ($word) use ($punctuationMarks) {
        return mb_strtolower(trim($word, implode('',$punctuationMarks)));
    }, $filtered);

    $countByWord = [];
    foreach ($preparedWords as $word) {
        if (array_key_exists($word, $countByWord)){
            $countByWord[$word] += 1;
        } else {
            $countByWord[$word] = 1;
        }
    }
    arsort($countByWord);

    return array_slice($countByWord,0,5);
}
```
# 2
1. Создать канвас 1000x1000
2. Внутри создать прямоугольник посередине 200x100
3. Сделать кнопку которая увеличит прямоугольник до 300х200 и повернет его на 45 градусов при этом он должен остаться также в середине
    
**Решение:**
    Для удобства по отдельной ссылке
https://codepen.io/kudrvet/pen/dyvPywy

# 3
Написать запрос для получения из таблицы с продуктами по 2 последних товара из каждой коллекции без использования HAVING.

![image](https://user-images.githubusercontent.com/25493836/117374676-3deb5780-aed6-11eb-8ca4-d5eb899495e7.png)

**Решение:**
```sql
select t1.* from orders t1
where (select count(*) from orders t2 where t1.Id_collection=t2.Id_collection and t2.date_added > t1.date_added) < 2;
```
