# Паттерны проектирования: паттерн Стратегия в TypeScript

### Как использовать и реализовать паттерн Стратегия в TypeScript для решения реальных проблем в веб-проектах.

[Источник](https://javascript.plainenglish.io/design-patterns-strategy-pattern-in-typescript-54eda9b40f09)

<img src="./images/img1.jpg" />
<br />

Добро пожаловать в серию **«Паттерны проектирования в TypeScript»**, в которой будет представлен ряд полезных паттернов проектирования в веб-разработке с использованием TypeScript.

Вот ссылки на опубликованные статьи:

- [Паттерн Стратегия в TypeScript](https://javascript.plainenglish.io/design-patterns-strategy-pattern-in-typescript-54eda9b40f09)
- [Паттерн Цепочка Обязанностей в TypeScript](https://javascript.plainenglish.io/design-patterns-chain-of-responsibility-pattern-in-typescript-dba6bdffe456)
- [Паттерн Наблюдатель в TypeScript](https://javascript.plainenglish.io/design-patterns-observer-pattern-in-typescript-f6589f1ce4fc)
- [Паттерн Шаблонного метода в TypeScript](https://javascript.plainenglish.io/design-patterns-template-method-pattern-in-typescript-ce0c8b158985)
- [Паттерн Адаптер в TypeScript](https://javascript.plainenglish.io/design-patterns-adapter-pattern-in-typescript-4b7ad3c1c234)
- [Паттерн Фабричного метода в TypeScript](https://javascript.plainenglish.io/design-patterns-factory-method-pattern-in-typescript-c4c3047a6289)
- [Паттерн Абстрактной фабрики в TypeScript](https://javascript.plainenglish.io/design-patterns-abstract-factory-pattern-in-typescript-84cd7b002964)

Паттерны проектирования очень важны для веб-разработчиков, которые освоив паттерны становятся способными улучшить качество написания кода. В этой статье я буду использовать **TypeScript**, чтобы рассказать о **паттерне Стратегия**.

Важным функционалом веб-приложений является регистрация и вход в систему. Для регистрации в веб приложении наиболее распространенным способом является использование учетной записи/пароля, электронной почты или номера мобильного телефона. Регистрация и вход в систему являются важными функциями веб-приложений. При регистрации веб-приложения более распространенным способом регистрации является использование учетной записи/пароля, электронной почты или мобильного телефона. И после успешной регистрации пользователь может использовать соответствующий метод для входа (аутентификации).

```
function login(mode) {
  if (mode === "account") {
    loginWithPassword();
  } else if (mode === "email") {
    loginWithEmail();
  } else if (mode === "mobile") {
    loginWithMobile();
  }
}
```

Иногда веб-приложение может поддерживать другие методы входа, например, в дополнение к методу входа по электронной почте, страница входа на Medium также поддерживает методы входа сторонних платформ, таких как Google, Facebook, Apple и Twitter.

<img src="./images/img2.png" />
<br />

В дальнейшем, для поддержки новых сторонних методов входа нам нужно будет снова и снова изменять и дополнять существующую `login` функцию, которая обслуживает процесс входа в веб-приложение:

```
function login(mode) {
  if (mode === "account") {
    loginWithPassword();
  } else if (mode === "email") {
    loginWithEmail();
  } else if (mode === "mobile") {
    loginWithMobile();
  } else if (mode === "google") {
    loginWithGoogle();
  } else if (mode === "facebook") {
    loginWithFacebook();
  } else if (mode === "apple") {
    loginWithApple();
  } else if (mode === "twitter") {
    loginWithTwitter();
  }
}
```

Если мы продолжим и далее добавлять или изменять методы входа в систему, мы обнаружим, что функцию `login` входа в систему становится все труднее поддерживать. И для решения данной проблемы можно использовать паттерн Стратегия, в котором мы сможем инкапсулировать различные методы входа в веб-приложение в разные стратегии входа.

Чтобы лучше понять код, который я приведу далее, давайте сначала посмотрим на соответствующую диаграмму UML: <img src="./images/img3.png" /> <br />

На приведенном выше рисунке мы определяем интерфейс `Strategy`, а затем на основе этого интерфейса `Strategy` реализуем две стратегии входа - через **Twitter** и **через учетную запись/пароль**.

**Интерфейс Strategy**

```
interface Strategy {
  authenticate(args: any[]): boolean;
}
```

**Twitter Strategy класс**

```
class TwitterStrategy implements Strategy {
  authenticate(args: any[]) {
    const [token] = args;
    if (token !== "tw123") {
      console.error("Twitter account authentication failed!");
      return false;
    }
    console.log("Twitter account authentication succeeded!");
    return true;
  }
}
```

**LocalStrategy класс**

```
class LocalStrategy implements Strategy {
  authenticate(args: any[]) {
    const [username, password] = args;
    if (username !== "bytefer" && password !== "666") {
      console.log("Incorrect username or password!");
      return false;
    }
    console.log("Account and password authentication succeeded!");
    return true;
  }
}
```

После того как мы описали различные стратегии входа в систему, мы определяем класс Authenticator для переключения между различными стратегиями входа и выполнения соответствующих операций аутентификации.

**Класс Authenticator**

```
class Authenticator {
  strategies: Record<string, Strategy> = {};
  use(name: string, strategy: Strategy) {
    this.strategies[name] = strategy;
  }
  authenticate(name: string, ...args: any) {
    if (!this.strategies[name]) {
      console.error("Authentication policy has not been set!");
      return false;
    }
    return this.strategies[name].authenticate.apply(null, args);
  }
}
```

После этого мы можем использовать различные методы входа для аутентификации пользователя с помощью следующего кода:

```
const auth = new Authenticator();
auth.use("twitter", new TwitterStrategy());
auth.use("local", new LocalStrategy());
function login(mode: string, ...args: any) {
  return auth.authenticate(mode, args);
}
login("twitter", "123");
login("local", "bytefer", "666");
```

Когда вы запустите приведенный выше код, вы получите соответствующий ему вывод, показанный ниже на рисунке: <img src="./images/img4.png" /> <br />

Кроме задачи аутентификации при входе паттерн Стратегия можно использовать при решении задач проверки полей формы. А также - для оптимизации проблем со слишком большим количеством ветвей if else.

Если вы используете Node.js для разработки сервисов аутентификации, обратите внимание на модуль [passport.js](https://www.passportjs.org/):

> **[!WARNING] ЗДЕСЬ ПЕРЕКРЕСТНАЯ ССЫЛКА НА САЙТ passportjs.org.**

Этот модуль очень мощный и в настоящее время поддерживает до 538 стратегий аутентификации: <img src="./images/img5.png" /> <br />

Итак, подытожим сценарии использования паттерна Стратегия:

- Когда системе необходимо динамически выбирать один из нескольких алгоритмов, каждый алгоритм может быть инкапсулирован в отдельный, созданный для него, класс стратегии.
- Когда несколько классов отличаются только поведением, и вы сможете использовать паттерн Стратегия для динамического выбора конкретного поведения, которое будет выполняться во время выполнения. <br /> Если у вас есть какие-либо вопросы, пожалуйста, пишите мне. В дальнейшем я продолжу знакомить вас с другими паттернами, и если вам интересно, подпишитесь на меня в Medium или Twitter.
