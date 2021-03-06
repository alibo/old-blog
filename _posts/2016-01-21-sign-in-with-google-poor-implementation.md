---
layout: post
comments: true
title: پیاده سازی ضعیف در ورود با گوگل!
image: assets/images/2016-01-21_toggl_login.jpg
tags: 
  - sign-in with google
  - oauth2
  - ورود با گوگل
---

![صفحه لاگین سرویس toggl.com]({{site.baseur}}/assets/images/2016-01-21_toggl_login.jpg)
*صفحه لاگین سرویس toggl.com*

امروزه بیشتر اپ ها و وبسایت ها به کاربرانشان اجازه می‌دهند با استفاده از اکانت گوگل، فیسبوک، توییتر و ... بتوانند راحت‌تر ثبت نام کرده و وارد اپ یا وبسایت شوند. اما بعضی از پیاده سازی ها نه تنها باعث راحتی کاربر نشده بلکه مشکلات را بیشتر می کنند!

<!-- more -->

به عنوان مثال، من در سرویس [toggl](https://toggl.com) ثبت‌نام کرده‌ام اما نه از طریق ورود با گوگل، بلکه فرم ثبت نام را پر کردم و ایمیل ارسالی را هم تایید کردم! *دلیل اینکه از ورود با گوگل استفاده نکرده ام به خاطر این هست که طراحی قدیمی این سایت دکمه ورود با گوگل را طوری گذاشته بود که براحتی دیده نمی شد!!* بگذریم، من با آدرس جیمیل در این سایت ثبت نام کرده ام، در نتیجه انتظارم این هست که می توانم از طریق دکمه ورود با گوگل هم وارد سایت شوم، اما انتظار اشتباهی دارم!!

![خطا در هنگام ورود با گوگل در سرویس toggl]({{site.baseurl}}/assets/images/2016-01-21_toggl_sign_in_with_google_failed.jpg)

چندین بار تلاش کردم، اما به نتیجه نرسیدم و در حال حاضر مجبورم همیشه با ایمیل و پسورد وارد حسابم شوم!


مثال دیگر سایتی بود که چند ماه پیش دیدم، متاسفانه آدرس آن یادم نیست، که امکان ورود از طریق چند سایت از جمله گوگل را نیز داشت، من ورود با گوگل را زدم، به سایت بازگشت، حالا پیامی عجیب به من نشان داد:

> به اینباکس ایمیل خود رفته و ایمیل خود را تایید کنید!

البته این ترجمه چیزی هست که به یاد می آورم! اولین سوالی که برای من پیش آمد این بود که اگر قرار است که دوباره ایمیل خود را تایید کنم دیگر چه نیازی به استفاده از ورود با گوگل است؟! یعنی مشکل من نوشتن اسم و فامیلم هست؟!!
کمی در مورد [API ورود و احراز هویت گوگل](https://developers.google.com/identity/protocols/OAuth2) تحقیق کردم و خودم آن را تست کردم ببینم چه چیزی را بر می گرداند. بعد از گرفتن `access_token` و ارسال درخواست `GET` به `https://www.googleapis.com/userinfo/v2/me` جواب زیر را گرفتم:

{% highlight json %}
{
  "family_name": "Borhani",
  "name": "Ali Borhani",
  "picture": "https://lh6.googleusercontent.com/-**********/AAAAAAAAAAI/AAAAAAAAAnQ/***********/photo.jpg",
  "locale": "en",
  "gender": "male",
  "email": "aliborhani1<atـgmail>",
  "link": "https://plus.google.com/+AliBorhani1",
  "given_name": "Ali",
  "id": "**********************",
  "verified_email": true
}
{% endhighlight %}


*مقدار ایمیل برای دوستان اسمپر تغییر داده شده!!*

مقدار `email` که همان ایمیل اصلی من هست و در صورتی که از جیمیل استفاده کنم، قابل تغییر نیست. [اطلاعات بیشتر](https://support.google.com/accounts/answer/6316959?p=emails&hl=en&rd=1)

`verified_email` هم یکی از فیلدهای جالب هست، اکانت های گوگلی که با استفاده از آدرس جیمیل ایجاد نشده‌اند، نیاز به تایید ایمیلشان هست. [اطلاعات بیشتر](https://support.google.com/accounts/answer/63950?hl=en)

حالا برگردیم به مثال اول، آیا نمی توان مقدار ایمیل را در دیتابیس جستجو کرد و کاربر را پیدا کرد!؟ حتما باید ایمیل و پسورد وارد شود هنگامی که گوگل آن را تایید کرده است؟
اگر از فیسوک و توییتر و گیت‌هاب و ... برای ورود استفاده کنیم، باید حتما کاربر همان وبسایتی را استفاده کند که برای بار اول با آن ثبت‌نام کرده است یا مثل سرویس [bitbucket](https://bitbucket.org/)  از هر سرویسی که ایمیل در آن مشترک است، می توان استفاده کرد؟! مطمئنا سناریو آخر، جواب سوال است.

اما برای مثال دوم، اگر معتبر بودن ایمیل برای ما ضروری است می‌توان `verified_email` را هم چک کرد، در صورتی که مقدار آن برابر با `false` باشد، می توان یک ایمیل تایید نیز برای آن آدرس ارسال کرد.

------

در نظر گرفتن سناریوهای مختلف، باعث می شود تجربه کاربری بهتری برای کاربرانمان بسازیم. همچنین بهتر است توسعه‌دهندگان با پروتکل هایی مثل OAuth2 و روند احراز هویت و همچنین API ها و مستندات آنها در سرویس‌های مختلف بیشتر آشنا شوند.

------

در این زمینه لینک‌های زیر را هم پیشنهاد می‌کنم مشاهده کنید:

- [Google - OAuth 2.0 Playground](https://developers.google.com/oauthplayground/)
- [Google API Explorer](https://developers.google.com/apis-explorer/)

با دو سرویس بالا راحت می‌توانید API های گوگل را تست کنید، بدون دردسر و خونریزی!!

- [Laravel Socialite](https://github.com/laravel/socialite)

اگر از فریمورک لاراول استفاده می کنید، پیشنهاد می‌کنم از پکیج رسمی خود لاراول برای ورود با گوگل و سرویس‌های دیگر استفاده کنید و زمان خود را صرف کارهای مهمتر کنید!!

سرویس‌هایی که پشتبیانی می‌شوند:

- فیسبوک
- گوگل
- توییتر
- لیندکین
- بیت‌باکت
- گیت‌هاب

همچنین می توانید با implement کردن [اینترفیس‌های آن](https://github.com/laravel/socialite/tree/2.0/src/Contracts) ، سایر سرویس‌ها را نیز پوشش دهید.
