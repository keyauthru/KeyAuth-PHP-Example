# KeyAuth-PHP-Пример
Пример PHP для https://keyauth.ru система аутентификации.

## ** Видеоурок:**

<a href="http://www.youtube.com/watch ?feature=player_embedded&v=hU6yXGR5R1Y
" target="_blank"><img src="https://i.imgur.com/IFHFaiI.png " 
alt="Нажмите здесь, чтобы посмотреть видео установки!" ширина="500" высота="250" граница="10" /></a>

## **Ошибки**

Если пример по умолчанию, не добавленный в ваше программное обеспечение, работает неправильно, пожалуйста, присоединяйтесь к серверу Discord https://discord.gg/keyauth и отправьте сообщение о проблеме в канале `#ошибки`.

Однако мы **НЕ** предоставляем поддержку для добавления KeyAuth в ваш проект. Если вы не можете в этом разобраться, вам следует воспользоваться Google или YouTube, чтобы узнать больше о языке программирования, на котором вы хотите продавать программу.

## Лицензия на авторское право

KeyAuth лицензирован по **эластичной лицензии 2.0**

* Вы не имеете права предоставлять программное обеспечение третьим лицам в качестве размещенного или управляемого
сервиса, если сервис предоставляет пользователям доступ к какому-либо существенному набору
функций программного обеспечения.

* Вы не имеете права перемещать, изменять, отключать или обходить функциональность лицензионного ключа
в программном обеспечении, и вы не имеете права удалять или скрывать какие-либо функциональные
возможности программного обеспечения, защищенные лицензионным ключом.

* Вы не имеете права изменять, удалять или скрывать какие-либо уведомления о лицензировании, авторских правах или другие уведомления
лицензиара в программном обеспечении. Любое использование товарных знаков лицензиара регулируется
применимым законодательством.

Благодарим вас за ваше согласие, мы усердно работаем над разработкой KeyAuth и не одобряем нарушения наших авторских прав.

## Защита от DDoS-атак 

Пожалуйста, добавьте правило брандмауэра Cloudflare, чтобы показывать пользователям challenge, затем установите время прохождения challenge равным 1 году, чтобы им не приходилось часто проходить challenge
![image](https://user-images.githubusercontent.com/83034852/168191187-236e8be7-1b1c-4398-9360-462baa800fac.png)
![image](https://user-images.githubusercontent.com/83034852/168191204-d553f134-943b-466e-a98f-255fbab204c6.png)


## **Определение экземпляра `KeyAuthApp`**

Визит https://keyauth.ru/app / и выберите свое приложение, затем перейдите на вкладку **PHP**

Это предоставит вам код, который вы должны заменить в `credentials.php ` файл.

```php
$KeyAuthApp = new KeyAuth\api("appNameHere", "keyAuthOwnerIDHere");
```

## **Инициализировать приложение**

Вы должны вызвать эту функцию перед использованием любой другой функции проверки подлинности ключа. В противном случае другая функция аутентификации по ключу не будет работать.

```php
$KeyAuthApp->init();
```

## **Отображение информации о приложении**

```php
$numKeys = $_SESSION["numUsers"];
$numUsers = $_SESSION["numKeys"];
$numOnlineUsers = $_SESSION["numOnlineUsers"];
$customerPanelLink = $_SESSION["customerPanelLink"];
```

## **Войдите в систему с помощью имени пользователя/пароля**

```php
if ($KeyAuthApp->login("userNameHere", "passWordHere")) {
  // send user to dashboard or wherever you prefer
}
```

## **Зарегистрируйтесь с помощью имени пользователя/пароля/ключа**

```php
if ($KeyAuthApp->register("userNameHere", "passWordHere", "licenseKeyHere")) {
  // send user to dashboard or wherever you prefer
}
```

## ** Обновить имя пользователя/ключ**

Используется для того, чтобы пользователь мог добавить дополнительное время к своей учетной записи, запросив новый ключ.

> **Предупреждение**
> Для обновления учетной записи пароль не требуется. Таким образом, в отличие от функций входа в систему, регистрации и лицензирования - вы не должны **** входить в систему пользователя после успешного обновления.

```php
if ($KeyAuthApp->upgrade("userNameHere", "licenseKeyHere")) {
			// don't login, upgrade function is not for authentication, it's simply for redeeming keys
      // make the user login with their username and password now.
}
```

## **Войдите в систему, используя только лицензионный ключ**

Пользователи могут использовать эту функцию, если их лицензионный ключ никогда ранее не использовался и если он использовался ранее. Поэтому, если вы планируете просто разрешить пользователям использовать ключи, вы можете удалить функции входа в систему и регистрации из своего кода.

```php
if ($KeyAuthApp->license("licenseKeyHere")) {
      // send user to dashboard or wherever you prefer
}
```

## **Пользовательские данные**

Отображать информацию для текущего пользователя, вошедшего в систему.

```php
$username = $_SESSION["user_data"]["username"];
$subscriptions = $_SESSION["user_data"]["subscriptions"];
$subscription = $_SESSION["user_data"]["subscriptions"][0]->subscription;
$expiry = $_SESSION["user_data"]["subscriptions"][0]->expiry;
for ($i = 0; $i < count($subscriptions); $i++) {
    echo "#" . $i + 1 . " Subscription: " . $subscriptions[$i]->subscription . " - Subscription Expires: " . "<script>document.write(convertTimestamp(" . $subscriptions[$i]->expiry . "));</script>";
}
```

## **Проверьте имя пользователя для подписки**

Если вы хотите запретить доступ к частям вашего приложения только определенным пользователям, у вас может быть несколько подписок с разными именами. Затем, когда вы создадите лицензии, соответствующие уровню этой подписки, пользователи, которые используют эти лицензии, получат подписку с названием подписки, соответствующим уровню используемого ими лицензионного ключа.

```php
if(findSubscription("default", $_SESSION["user_data"]["subscriptions"])) {
    // user has subscription with name "default"
}
else {
    // user does not have subscription with name "default"
}
```

## **Переменные приложения**

Строка, которая хранится на стороне сервера Key Auth. На панели мониторинга вы можете выбрать для каждой переменной аутентификацию (доступ могут получить только зарегистрированные пользователи) или не аутентификацию (любой пользователь может получить доступ до входа в систему). Они являются глобальными и статичными для всех пользователей, в отличие от пользовательских переменных, которые будут рассмотрены ниже в этом разделе.

```php
//* Get Public Variable
$var = $KeyAuthApp->var("varName");
echo "Variable Data: " . $var;
```

## **Пользовательские переменные**

Пользовательские переменные - это строки, хранящиеся на стороне сервера Key Auth. Они специфичны для пользователей. Их можно установить на панели мониторинга на вкладке "Пользователи", через SellerAPI или через ваш загрузчик, используя приведенный ниже код. "discord" - это имя пользовательской переменной, по которому вы извлекаете пользовательскую переменную. `test#0001` - это данные переменной, которые вы получаете при извлечении пользовательской переменной.

```php
//* Set Up User Variable
$KeyAuthApp->setvar("varName", "varData");
```

И вот как вы извлекаете пользовательскую переменную:

```php
//* Get User Variable
$var = $KeyAuthApp->getvar("varName");
echo "Variable Data: " . $var;
```

## **Журналы приложений**

Может использоваться для регистрации данных. Хорошо подходит для предупреждений об отладке и, возможно, для отладки ошибок. Если вы настроите Discord webhook в настройках приложений панели мониторинга, он будет отправлять сообщения журнала на ваш Discord webhook, а не сохранять их на сайте. Рекомендуется установить Discord webhook, так как логи на сайте удаляются через 1 месяц после отправки.

Вы можете использовать функцию регистрации до входа в систему и после входа в систему.

```php
//* Log Something to the KeyAuth webhook that you have set up on app settings
$KeyAuthApp->log("message");
```


## Веб-хуки на стороне сервера

Обучающее видео https://www.youtube.com/watch?v=ENRaNPPYJbc

> **Примечание**
> Ознакомьтесь с документацией по веб-подключениям KeyAuth здесь https://keyauth.readme.io/reference/introduction

Безопасно отправляйте HTTP-запросы по URL-адресам, не допуская утечки URL-адреса в вашем приложении. Вам обязательно следует использовать, если вы хотите отправлять запросы в SellerAPI из своего приложения, в противном случае, если вы этого не сделаете, вы передадите свой ключ продавца всем. И тогда кто-то может испортить ваше приложение.

1-й пример - это как отправить запрос без данных POST. просто запрос GET на URL-адрес. `7kR0UedlVI` - это идентификатор веб-узла, `https://keyauth.ru/api/seller/?sellerkey=sellerkeyhere&type=black ` - это то, что вы должны указать в качестве конечной точки webhook на панели мониторинга. Это та часть, которую вы не хотите, чтобы пользователи видели. И тогда у вас есть `&ip=1.1.1.1&hwid=abc` в вашем программном коде, который будет добавлен к конечной точке webhook на сервере keyauth, а затем запрос будет отправлен.

2-й пример включал данные post, JSON. Это пример запроса к Discord webhook `7kR0UedlVI` - это идентификатор webhook, `https://discord.com/api/webhooks /...` - это конечная точка webhook.

```php
$result = $KeyAuthApp->webhook("7kR0UedlVI", "&ip=1.1.1.1&hwid=abc");
echo "<br> Result from Webhook: " . $result;

$result = $KeyAuthApp->webhook("7kR0UedlVI", "", "{\"content\": \"webhook message here\",\"embeds\": null}", "application/json"); // if Discord webhook message successful, response will be empty
echo "<br> Result from Webhook: " . $result;
```

## Забанить пользователя

Заблокируйте пользователя и внесите в черный список его HWID и IP-адрес.

Функция работает только после входа в систему.

Параметром reason будет причина запрета, отображаемая пользователю при попытке входа в систему и видимая на панели управления KeyAuth.

```php
$KeyAuthApp->ban('Broke the rules');
```
