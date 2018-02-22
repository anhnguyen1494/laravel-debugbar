## Laravel Debugbar
[![Packagist License](https://poser.pugx.org/barryvdh/laravel-debugbar/license.png)](http://choosealicense.com/licenses/mit/)
[![Latest Stable Version](https://poser.pugx.org/barryvdh/laravel-debugbar/version.png)](https://packagist.org/packages/barryvdh/laravel-debugbar)
[![Total Downloads](https://poser.pugx.org/barryvdh/laravel-debugbar/d/total.png)](https://packagist.org/packages/barryvdh/laravel-debugbar)

### Lưu ý cho v3: Debugbar hiện tại đã được kích hoạt khi dùng gói, nhưng mặc định vẫn cần APP_DEBUG = true!

### Đối với Laravel < 5.5, hãy dùng  [2.4 branch](https://github.com/barryvdh/laravel-debugbar/tree/2.4)!

Đây là gói để tích hợp [PHP Debug Bar](http://phpdebugbar.com/) với Laravel 5.
Nó bao gồm ServiceProvider để đăng kí debugbar và kèm theo đầu ra.
Bạn có thể xuất file cấu hình và cấu hình nó thông qua Laravel.
Nó có một số Collectors để làm việc với Laravel và thực hiện song song với DataCollectors tùy chỉnh, cụ thể với Laravel.
Nó được cấu hình để hiện thị Redirects và (jQuery) Ajax Requests. (Hiện thị theo dropdown)
Đọc [tài liệu](http://phpdebugbar.com/docs/) để biết thêm các tùy chọn cấu hình.

![Screenshot](https://cloud.githubusercontent.com/assets/973269/4270452/740c8c8c-3ccb-11e4-8d9a-5a9e64f19351.png)

So when experiencing slowness, try disabling some of the collectors.
Chú ý: Chỉ sử dụng DebugBar trong môi trường dev. Nó có thể làm chậm ứng dụng một chút (Bởi vì nó phải thu thập dữ liệu). Vì vậy, khi gặp tình trạng chậm này, hãy thử vô hiệu hóa một số collectors.

Package này bao gồm một số collectors tùy chỉnh:
 - QueryCollector: Hiển thị tất cả truy vấn, bao gồm ràng buộc và thời gian thực hiện truy vấn 
 - RouteCollector: Hiển thị thông tin về Route hiện tại.
 - ViewCollector: Hiển thị các view đang được tải. (Tùy chọn: hiển thị dữ liệu được chia sẻ)
 - EventsCollector: Hiển thị tất cả sự kiện.
 - LaravelCollector: Hiển thị phiên bản Laravel và Environment. (mặc định tắt)
 - SymfonyRequestCollector: Thay thế RequestCollector với nhiều thông tin hơn về request và response
 - LogsCollector: Hiển thị các log mới nhất từ storage logs. (mặc định tắt)
 - FilesCollector: Hiển thị các tệp được included/required bởi PHP. (mặc định tắt)
 - ConfigCollector: Hiển thị các giá trị từ các file cấu hình. (mặc định tắt)
 - CacheCollector: Hiển thị sự kiện cache. (mặc định tắt)

Bootstraps hỗ trợ mốt số collectors cho Laravel:
 - LogCollector: Hiển thị tất cả Log messages
 - SwiftMailCollector và SwiftLogCollector cho Mail

Và một số collectors mặc định:
 - PhpInfoCollector
 - MessagesCollector
 - TimeDataCollector (Với thời gian khởi động và thực thi)
 - MemoryCollector
 - ExceptionsCollector

Nó còn cung cấp một giao diện để đơn giản cho Messages, Exceptions và Time

## Cài đặt

Tích hợp gói này với composer. Chỉ nên tích hợp gói cho môi trường dev.

```shell
composer require barryvdh/laravel-debugbar --dev
```

Laravel 5.5 sử dụng Package Auto-Discovery,do đó không cần tự thêm vào ServiceProvider.

Debugbar sẽ kích hoạt khi `APP_DEBUG` là `true`.

> Nếu bạn sử dụng catch-all/fallback route, đảm bảo bạn tải Debugbar ServiceProvider trước các App ServiceProviders của riêng bạn.

### Laravel 5.5+:

Nếu bạn không sử dụng auto-discovery, thêm ServiceProvider vào mảng providers trong config/app.php

```php
Barryvdh\Debugbar\ServiceProvider::class,
```

Nếu bạn muốn sử dụng facade để ghi lại các tin nhắn, thêm vào facades của bạn trong app.php:

```php
'Debugbar' => Barryvdh\Debugbar\Facade::class,
```

Debugbar đã mặc định kích hoạt, nếu bạn có APP_DEBUG=true. Bạn có thể ghi đè nó trong cấu hình (`debugbar.enabled`) hoặc tùy chỉnh `DEBUGBAR_ENABLED` trong `.env` của bạn. Tham khảo thêm tùy chọn trong `config/debugbar.php`
Bạn cũng có thể đặt trong cấu hình của bạn nếu bạn muốn include/exclude các tập tin từ vendor (FontAwesome, Highlight.js and jQuery). Nếu bạn đã sử dụng nó trong trang của bạn, đặt nó là false.
Bạn cũng có thể chỉ hiện thị  js hoặc css vendors, bằng cách đặt nó vào 'js' hoặc 'css'. (Highlight.js yêu cầu đồng thời css + js, do đó đặt `true` để tô sáng cú pháp)

Copy cấu hình package sang cấu hình cục bộ với command:

```shell
php artisan vendor:publish --provider="Barryvdh\Debugbar\ServiceProvider"
```

### Lumen:

Đối với Lumen, đăng kí Provider khác vào trong `bootstrap/app.php`:

```php
if (env('APP_DEBUG')) {
 $app->register(Barryvdh\Debugbar\LumenServiceProvider::class);
}
```

Để thay đổi cấu hình, copy file vào folder cấu hình của bạn và kích hoạt nó:

```php
$app->configure('debugbar');
```

## Sử dụng

Bây giờ bạn có thể thêm các message bằng cách sử dụng Facade (khi thêm), sử dụng các mức độ PSR-3 (debug, info, notice, warning, error, critical, alert, emergency):

```php
Debugbar::info($object);
Debugbar::error('Error!');
Debugbar::warning('Watch out…');
Debugbar::addMessage('Another message', 'mylabel');
```

Và bắt đầu/dừng thời gian:

```php
Debugbar::startMeasure('render','Time for rendering');
Debugbar::stopMeasure('render');
Debugbar::addMeasure('now', LARAVEL_START, microtime(true));
Debugbar::measure('My long operation', function() {
    // Do something…
});
```

Hoặc log ngoại lệ:

```php
try {
    throw new Exception('foobar');
} catch (Exception $e) {
    Debugbar::addThrowable($e);
}
```

There are also helper functions available for the most common calls:
Ngoài ra cũng có các hàm helper có sẵn phổ biến nhất:

```php
// All arguments will be dumped as a debug message
debug($var1, $someString, $intValue, $object);

start_measure('render','Time for rendering');
stop_measure('render');
add_measure('now', LARAVEL_START, microtime(true));
measure('My long operation', function() {
    // Do something…
});
```

Nếu bạn muốn bạn có thể thêm DataCollectors của riêng bạn, thông qua Container hoặc Facade:

```php
Debugbar::addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
//Or via the App container:
$debugbar = App::make('debugbar');
$debugbar->addCollector(new DebugBar\DataCollector\MessagesCollector('my_messages'));
```

Mặc định, Debugbar được chèn ngay trước `</body>`. Nếu bạn muốn tự mình chèn Debugbar,
đặt tùy chọn 'inject' là false và sử dụng rendering và hãy tham khảo http://phpdebugbar.com/docs/rendering.html

```php
$renderer = Debugbar::getJavascriptRenderer();
```

Chú ý: Không sử dụng auto-inject, nó sẽ vô hiệu hóa thông tin Request, bởi vì nó được thêm vào sau phản hồi.
Bạn có thể thêm default_request datacollector trong cấu hình dưới dạng thay thế.

## Bật / Tắt trong khi chạy
Bạn có thể bật hoặc tắt thanh debugbar trong thời gian chạy.

```php
\Debugbar::enable();
\Debugbar::disable();
```

NB. Khi được kích hoạt, các collectors được thêm vào (và có thể sinh ra thêm trên đầu), vì vậy bạn muốn sử dụng debugbar trong môi trường production, tắt trong cấu hình và chỉ bật khi cần.


## Tích hợp Twig

Laravel Debugbar đi kèm với 2 Twig Extensions. Đây là bản thử nghiệm với [rcrowe/TwigBridge](https://github.com/rcrowe/TwigBridge) 0.6.x

Thêm các phần mở rộng TwigBridge sau vào  config/extensions.php (hoặc tự mình đăng kí phần mở rộng)

```php
'Barryvdh\Debugbar\Twig\Extension\Debug',
'Barryvdh\Debugbar\Twig\Extension\Dump',
'Barryvdh\Debugbar\Twig\Extension\Stopwatch',
```

Phần mở rộng Dump sẽ thay thế [dump function](http://twig.sensiolabs.org/doc/functions/dump.html) cho biến đầu ra sử dụng DataFormatter. Phần mở rộng Debug thêm hàm `debug()`  chuyển các biến đến Message Collector,
thay vì hiển thị nó trực tiếp trong mẫu. Nó bỏ các đối số, hoặc khi rỗng; tất cả các biến context.

```twig
{{ debug() }}
{{ debug(user, categories) }}
```

Tiện ích Stopwatch thêm [stopwatch tag](http://symfony.com/blog/new-in-symfony-2-4-a-stopwatch-tag-for-twig)  tương tự như trong Symfony/Silex Twigbridge.

```twig
{% stopwatch "foo" %}
    …some things that gets timed
{% endstopwatch %}
```
