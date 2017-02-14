#yii2自带的验证码使用注意点

控制器`controller`中代码片断：

```php
class PublicController extends \yii\web\Controller {

    // 定义actionCaptcha
    public function actions() {
        return [
            'captcha' => [
                'class' => 'yii\captcha\CaptchaAction',
                'minLength' => 4,
                'maxLength' => 4,
                //'fixedVerifyCode' => YII_ENV_TEST ? 'testme' : null,
            ],
        ];
    }

    // 渲染视图（视图中包括验证码）
    public function actionIndex() {
        $model = new \app\models\Customer();
        if (\Yii::$app->request->isPost) {
            if ($model->load(\Yii::$app->request->post()) && $model->validate()) {
                return $this->redirect(['/']);
            }
        }
        return $this->render('index', ['model' => $model]);
    }

}
```

model中代码片断：

```php
class Customer extends \yii\db\ActiveRecord {

    public $verifyCode;

    public function rules() {
        return [
            // 默认captchaAction是'site/captcha'，如果不是用的默认action要注意修改
            ['verifyCode', 'captcha', 'captchaAction'=>'/public/captcha', 'message' => '验证码错误'],
        ];
    }
}
```

视图中代码片断，`widget`中的配置项`captchaAction`项要与`model`中`rules`中的`captchaAction`保持一致：

```php
$form->field($model, 'verifyCode')->widget(Captcha::className(), [
    'template' => '{input}{image}',
    'captchaAction'=>'public/captcha', // 与model中rules中的验证保持一致性
    'imageOptions'=>['alt'=>'点击换图','title'=>'点击换图', 'style'=>'cursor:pointer'],
]);
```