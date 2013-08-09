Дизайн в строчках кода: Организация JavaScript
------------------------------------------------------------

Великолепный дизайн это результат заботливого и внимательного отношения к наиболее важным деталям, который в итоге приводит к полезному, понятному и, надо надеяться, красивому интерфейсу. Но обманывайте себя тем, что дизайн это удел только лишь дизайнеров.

Существует множество дизайнерских решений в програмировании, и говоря это я подразумеваю дизайн не пользовательского интерфейса, но дизайн самого кода.

Хорошо спроектированный код намного легче поддерживать, улучшать, расширять и также позволяет разработчикам быть эффективнее. Это приводит к тому, что больше внимания и энергии будет потрачено на значимые вещи, что в свою очередь осчастливит всех — пользователей, разработчиков и все остальные заинтересованные стороны.

Существуют три действительно важных высокоуровневых, языково-независимых аспекта дизайна кода:

1. Архитектура — программная база для всего массива кода. Правила, определяющие как различные компоненты (модели, представления и контроллеры) взаимодействуют друг с другом.
2. Поддерживаемость – насколько хорошо код может быть улучшен или расширена его функциональность?
3. Повторное использование — насколько возможно использовать заново отдельные компоненты приложения? Насколько гибка в настройке каждая отдельная реализация каждого компонента?

В неудачных языках, в данном случае, в JavaScript от вас потребуется немного дисциплины для написания удобного и приятного кода. JavaScript-окружение настолько всепрощающе, что можно «разбросать» куски кода по разным углам вашего приложения — устроить настоящий бардак в нём, но тем временем, приложение всё ещё будет работать. Грамотное планирование и разработка архитектуры на раннем этапе развития (и её соблюдение после создания) привнесёт в ваш код ограничения, которые позволят вам добиться согласованности.

Я хочу показать вам один проверенный временем паттерн разработки, которым горжусь, — паттерн «Модуль», чья расширяемая структура дополнит вашу архитектуру до состояния стройной, поддерживаемой и расширяемой программной базы. Мне нравится создавать модули с помощью системы jQuery-плагинов, так как это обеспечивает удивительную возможность повторного использования, набор удобных опций и превосходно созданное API.

Ниже по тексту я расскажу вам, как писать ваш код организуя его в модули, которые можно будет использовать в ваших следующих проектах.

## Паттерн «Модуль»

Существует *множество* дизайн-паттернов и столько же у ресурсов про них. [Эдди Османи (Addy Osmani)][1] написал [потрясающую (бесплатную!) книгу][2] о паттернах в JavaScript, которую я сильно рекомендую разработчикам любого уровня.

Паттерн [Модуль][3] простая структурная основа, которая поможет хранить ваш код в чистоте и порядке. «Модуль» это обычный literal-объект содержащий методы и свойства, и эта простота это лучшее, что может быть в этом модуле: даже кто-то совершенно незнакомый с традиционными паттернами разработки программного обеспечения будет иметь возможность взглянуть на код и сразу же понять, как этот код работает. 

В приложениях, использующих этот паттерн, каждый компонент разрабатывается как отдельный модуль. К примеру, чтобы создать функциональность автодополнения, вы создадите модуль для текстового поля и модуль для списка результатов. Два модуля будут работать вместе, но код для текстового поля не должен иметь представления о списке результатов и наоборот.

Декомпозиция кода в модули является причиной того, почему паттерн Модуль превосходно подходит для разработки стройной и надёжной архитектуры приложения.
Отоношения между компонентами отлично определены; за любую работу с текстовым полем ответственен соответствующий модуль, не переплетаясь с другими модулями — всё это приведёт вас к чистому и приятному коду.

Ещё одним плюсом модульного приложения является внутреняя поддержка без головной боли. Модули могут быть улучшены и оптимизированый без последствий для других частей приложения. 

Я использовал паттерн Модуль для базовой структуры [jPanelMenu][4] — jQuery-плагин, который я написал для создания на сайте off-canvas-меню. Я буду использовать его в качестве примера по созданию модуля.

## Создание модуля

Сначала я определю три метода и свойство, который я буду использовать, чтобы управлять взаимодействиями с системой меню.

    var jpm = {
        animated: true,
        openMenu: function( ) {
            …
            this.setMenuStyle( );
        },
        closeMenu: function( ) {
            …
            this.setMenuStyle( );
        },
        setMenuStyle: function( ) { … }
    };

Идея в том, чтобы разбить код вдребезги на самые маленькие кусочки, возможные для повторного использования. Я могу написать только один метод `toggleMenu()`, но создание двух методов `openMenu()` и `closeMenu()` обеспечит мне больше контроля и возможности для повторного использования.

Заметьте, что методы и свойства во время использования *внутри модуля* имеют префикс `this` (например вызов `setMenuStyle( )`) ключевое слово с помощью которго модель получает доступ к собственным полям.

Это базовая структура модуля. По мере необходимости вы можете продожать добавлять методы и свойства, но при этом структура никогда не станет сложнее. После создания структуры модуля, вы можете создать на основе этой базы слой для переиспользования — опции и внешнее API. 

## jQuery-плагины

Третий аспект хорошо спроектированного кода и возможно самый спорный: повторное использование. Эта часть будет с подводными камнями. В то время, когда существуют способы для построения и внедрения реиспользуемых модулей с помощью чистого JavaScript (мы на 90% к финишу), я же предпочитаю использовать jQuery-плагины для создания более сложных вещей по нескольким причинам. 

Самое важное, что это является формой очевидной и ненавязчивой коммуникации. Если вы используете jQuery для создания модуля, то вы обязаны сделать его очевидным. Создание jQuery-плагина это лучший способ указать не необходимость использования jQuery.

В дополнение к этому реализация модуля будет сочетаться с остальным jQuery-кодом. Это радует глаз, но также превосходно влияет на предсказуемость кода. Ещё один способ создать более удобный интерфейс для разработчиков.

До того как вы начнёте писать jQuery-плагин, удостоверьтесь, что плагин не конфликтует с остальными JavaScript-библиотеками, использующими $-нотацию. В реальности всё проще, чем это звучит

    (function($) {
        // код jQuery-плагина
    })(jQuery);

Затем, мы настраиваем наш плагин и внедряем написанный выше модуль внутрь jQuery-обёртки. jQuery-плагин — всего лишь метод объекта `$` (jQuery).

    (function($) {
        $.jPanelMenu = function( ) {
            var jpm = {
                animated: true,
                openMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                closeMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                setMenuStyle: function( ) { … }
            };
        };
    })(jQuery);

Всё это приводит к тому, что использовать наш плагин удобно как никогда — необходимо лишь вызвать только что созданный метод.

    var jpm = $.jPanelMenu( );

## настройки

Настройки жизнено необходимы для любого плагина от которого по настоящему требуется повторное использование, так как это позволит использовать его в любой ситуации. Каждый проект приносит с собой свои детали: особенные стили, виды взаимодействия и структуры данных на страницах. Гибкие настройки помогают убедиться в том, что вы можете адаптировать плагин с учётом ограничений проекта. 

Считается хорошим тоном обеспеивать хорошее или полезное стандартное значение для каждой конкретной настройки. Простейший способ реализовать это использовать jQuery-метод `$.extend()`, принимающий два и больше аргумента.

Первым аргументом следует передавать объект со всеми настройками и с установленными стандартными их значениями. Вторым аргументом следует передавать объект уточняющих настроек, передаваемых в плагин при его инициализации. Метод `$.extend()` объединит эти два объекта, перезаписывая стандартные настройки уточняющими. 

    (function($) {
        $.jPanelMenu = function(options) {
            var jpm = {
                options: $.extend({
                    'animated': true,
                    'duration': 500,
                    'direction': 'left'
                }, options),
                openMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                closeMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                setMenuStyle: function( ) { … }
            };
        };
    })(jQuery);

В то время как вы предоставляете полезные стандартные настройки для вашего плагина, также сами настройки своими названиями могут объяснять своё предназначение, что позволяет понять, что делает код с первого взгляда.

Старайтесь предоставлять все возможно потребующиеся настройки. Гибкость поможет внедрять плагин в последующие проекты, и тем более, что гибкие настройки никогда не причиняли вреда.

## API

Настройки это потрясающий способ, чтобы контролировать работу плагина. API, наоборот, позволяет расширять функциональность плагина, предоставляя методы и свойства.

С одной стороны важно предоставлять всё, что возможно с помощью API, но с другой стороны никому кроме вас необязательно знать все внутренние свойства и методы. Идеальная ситуация представляет собой то, что вы предоставляете разработчику только те свойства и методы, которые ему могут понадобиться.

В нашем примере, API должен содержать вызовы показа и скрытия меню, но ничего больше. Внутрений метод `setMenuStyle()` вызывается, в моменты взаимодействия с меню, но он не нужен сторонему разработчику и поэтому не доступен публично.

Разрабатывая API, следует возвращать объект с необходимыми методами и свойствами в конце кода плагина. Вы также можете связать свойства и методы возвращаемого объекта с свойствами самого модуля — в этом месте прекрасно организованная структура паттерна Модуль начинает светиться. 

    (function($) {
        $.jPanelMenu = function(options) {
            var jpm = {
                options: $.extend({
                    'animated': true,
                    'duration': 500,
                    'direction': 'left'
                }, options),
                openMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                closeMenu: function( ) {
                    …
                    this.setMenuStyle( );
                },
                setMenuStyle: function( ) { … }
            };
            return {
                open: jpm.openMenu,
                close: jpm.closeMenu,
                someComplexMethod: function( ) { … }
            };
        };
    })(jQuery);

API методы и свойства будут доступны через объект, возвращаемый плагином при его инициализации.

    var jpm = $.jPanelMenu({
        duration: 1000,
        …
    });
    jpm.open( );

## Улучшение интерфейсов для разработчиков

Руководствуясь небольшим количеством простых ограничений и правил, мы создали для себя расширяемый, годный для повторного использования плагин, который сделает нашу жизнь проще и лучше. Как и со всем, что мы делаем, поэкспериментируйте со структурой, чтобы понять подходит ли она вам, вашей команде и вашему рабочему процессу.

Каждый раз, когда я обнаруживаю себя пишущим что-нибудь, что может пригодиться ещё раз, я разбиваю это по модулям, основываясь на jQuery-плагинах. Лучшая часть такого метода это то, что это заставляет вас использовать и тестировать написанный вами код. Используя что-нибудь созданное самим собой, вы быстро найдёте слабые и сильные места, определите недочёты и сможете запланировать изменения. 

Этот процесс приводит к «финальной акту»: оттестированый код готов к открытию исходных кодов или к продаже. Я открыл мой (самый) удобный и выверенный плагин opensource-проект на [GitHub][5]

Даже если вы не создаёте что-то, что планируется показать всему миру, но всё ещё важно думать о дизайне кода. В будущем вы скажите себе самому «спасибо».

[1]: https://twitter.com/addyosmani "@addyosmani"
[2]: http://addyosmani.com/resources/essentialjsdesignpatterns/book/ "Essential JS Design Patterns"

[3]: http://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript
[4]: http://jpanelmenu.com/
[5]: https://github.com/acolangelo "github/acolangelo"