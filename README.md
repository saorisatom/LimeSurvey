# 1. The 'sendInvitationMenuTokenRender' Event
### Description
In this version of limesurvey, we added the 'sendInvitationMenuTokenRender' event. By adding this event we want everyone to be able to add Extra Invitation Or Reminder options to the 'Invite & Remind' dropdown list in the token view (After "Send Invitation Email", "Send Remind Email" and "Edit email model").

### How you can use this event
You can use this event in plugins like we have done with [Send SMS](http://nolink.com). This is an example of plugin called `Example` where the event is used to add the `Example Menu Item` which will launch the `customFunction` when the user will click on it.
To see the complete guide on how to write a plugin, click [here](https://manual.limesurvey.org/Plugins)
```php
class Example extends ls\pluginmanager\PluginBase {

    protected $storage = 'DbStorage';    
    static protected $description = 'Example plugin';
    
    protected $settings = array(
        'test' => array(
            'type' => 'string',
            'label' => 'Message'
        ),
        'messages' => array(
            'type' => 'list',
            'label' => 'messages',
            'items' => array(
                'number' => array(
                    'type' => 'int',
                    'label' => 'Index'
                ),
                'message' => array(
                    'type' => 'string',
                    'label' => 'Message'
                ),
                
            )
        )
    );
    
    public function __construct(PluginManager $manager, $id) {
        parent::__construct($manager, $id);     
        /**
         * Here you should handle subscribing to the events your plugin will handle
         */
        $this->subscribe('sendInvitationMenuTokenRender');
        $this->subscribe('beforeSurveySettings');
        $this->subscribe('newSurveySettings');
    }
    
    
    /*
     * Below are the actual methods that handle events
     */
    
    /**
     * This is where our 'sendInvitationMenuTokenRender' is handled to add a specific 
     * MenuItem to the 'Invitation&Reminder' dropdown in the token view. 
     * We add our custom menuItem by appending it to the '$event' object.
     */
    public function sendInvitationMenuTokenRender(){
    	$event = $this->getEvent();
        $surveyId = $event->get('surveyId');

        $href = Yii::app()->createUrl(
            'admin/pluginhelper',
            array(
                'sa' => 'sidebody',
                'plugin' => 'Example',
                'method' => 'customFunction',
                'surveyId' => $surveyId
            )
        );

        $menuItem = new MenuItem(array(
            'label' => 'Example Menu Item',
            'iconClass' => 'fa fa-table',
            'href' => $href
        ));

        $event->append('extraInvitationOptions', array($menuItem));
    }
    
    /**
     * This event is fired by the administration panel to gather extra settings
     * available for a survey.
     * The plugin should return setting meta data.
     * @param PluginEvent $event
     */
    public function beforeSurveySettings()
    {
        $event = $this->event;
        $event->set("surveysettings.{$this->id}", array(
            'name' => get_class($this),
            'settings' => array(
                'message' => array(
                    'type' => 'string',
                    'label' => 'Message to show to users:',
                    'current' => $this->get('message', 'Survey', $event->get('survey'))
                )
            )
         ));
    }
    
    public function newSurveySettings()
    {
        $event = $this->event;
        foreach ($event->get('settings') as $name => $value)
        {
            
            $this->set($name, $value, 'Survey', $event->get('survey'));
        }
    }

    /**
     * Custom Function which will be called when the user will click the menu Item added 
     * by the sendInvitationMenuTokenRender() function.
     *
     * In this function we render a specific view : 'Example'
     * @param  $surveyId Id of the survey
     * @return string
     */
    public function customFunction($surveyId)
    {
        return $this->renderPartial('ExampleView', array("surveyId" => $surveyId), true);
    }

}
```
Like any other event, we need to subscribe to the 'sendInvitationMenuTokenRender' event, that's what we do `$this->subscribe('sendInvitationMenuTokenRender');` in the code.

Next we write the function `sendInvitationMenuTokenRender()` where we add our specific Menu Item here labeled `Example Menu Item`. When the user will click on this menu item, the method `customFunction` will be called.

In the `customFunction` method, we choosed to render a specific view `ExampleView`. To see more on how to do render a view, click [here](https://manual.limesurvey.org/Add_new_menu_and_view_by_a_plugin). But notice you should have a `php` file called `ExampleView.php` where the view is defined in `HTML/PHP` format.


# 2. LimeSurvey Documentation : The Sophisticated online survey software
## About
Limesurvey is the number one open-source survey software.

Advanced features like branching and multiple question types make it a valuable partner for survey-creation.

### Demo

See our [Administration Demo](http://demo.limesurvey.org/index.php?r=admin/authentication/sa/login).
The credentials are prefilled, just click **Log in**

Or try taking one of our [test surveys](https://survey.limesurvey.org/index.php?sid=78184&lang=en)


## How to install

### Release
We try to publish a release every other day.
We recommend using those.

### Repository
You may want to use the plain repository, which is also possible.

Please be advised, that we sometimes push development versions into the repository, which may not be working correctly.

## Requirements

### Minimal
The absolute minimal requirements are:
 - Apache >= 2.4 | nginx >= 1.1 | any other php-ready webserver
 - php >= 5.4
    - with mbstring and pdo-database drivers
 - mysql >= 5.5.3 | pgsql >= 9 | mariadb >= 5.5  | mssql >= 2005

### Recommended
We recommend the following setup
 - nginx 1.4.6
 - php 5.6.x
    - with php-fpm, mbstring, gd2 with freetype, imap, ldap, zip, zlib and databse drivers
 - mysql 5.5.50

## Manual
for more information please refer to our [homepage](http://www.limesurvey.org), or have a look at the [manual](http://manual.limesurvey.org) 

## Licence
LimeSurvey software is licenced under the [GPL 2.0](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html).

Pictures and the LimeSurvey Logo are registered trademarks of LimeSurvey GmbH, Hamburg, Germany.