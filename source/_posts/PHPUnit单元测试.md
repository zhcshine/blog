---
title: PHPUnit单元测试
tags:
  - php
url: 93.html
id: 93
comments: false
categories:
  - 后端
date: 2016-11-10 10:31:18
---

### 安装

安装composer

    sudo apt-get install composer
    

安装PHPUnit

    sudo composer require --dev phpunit/phpunit
    

安装xdebug

    sudo apt-get install php-xdebug
    

### 配置

路径/phpunit.xml

    <?xml version="1.0" encoding="utf-8" ?>
    <phpunit bootstrap="tests/bootstrap.php">
       <testsuites>
           <testsuite name="whovian">
               <directory suffix="Test.php">
                   tests
               </directory>
           </testsuite>
       </testsuites>
        <filter>
            <whitelist>
                <directory>src</directory>
            </whitelist>
        </filter>
    </phpunit>
    

tests/bootstrap..php表示phpunit文件的路径  
filter元素列出需要分析代码覆盖度分析涵盖的目录  
whitelist元素告诉PHPUnit只分析src/目录中的代码  
tests/bootstrap.php

    <?php
    require dirname(__DIR__) . '/vendor/autoload.php';
    

src/Whovian.php

    <?php
    
    class Whovian
    {
      protected $favoriteDoctor;
    
      // 设置实例喜欢的医生
      public function __construct($favoriteDoctor)
      {
        $this->favoriteDoctor = (string)$favoriteDoctor;
      }
    
      // 返回一个字符串，说出实例最喜欢的医生
      public function say()
      {
        return 'The best doctor is ' . $this->favoriteDoctor;
      }
    
      // 与另一个实例喜欢的医生进行比较，并做出回应
      public function respondTo($input)
      {
        $input = strtolower($input);
        $myDoctor = strtolower($this->favoriteDoctor);
        if(strpos($input, $myDoctor) === false) {
          throw new Exception(sprintf(
            'No way, %s is the best doctor ever', $this->favoriteDoctor
          ));
        }
        return 'I agree!';
      }
    }
    

tests/WhovianTest.php

    <?php
    
      require dirname(__DIR__) . '/src/Whovian.php';
      class WhovianTest extends PHPUnit_Framework_TestCase
      {
        // 确认构造方法是否设定了Whovian实例最喜欢的医生
        public function testSetsDoctorWithConstructor()
        {
          $whovian = new Whovian('Peter Capaldi');
          $this->assertAttributeEquals('Peter Capaldi', 'favoriteDoctor', $whovian);
        }
    
        // 确认say方法是否会返回一个包含最喜欢医生名字的字符串
        public function testSaysDoctorName()
        {
          $whovian = new Whovian('David Tennant');
          $this->assertEquals('The best doctor is David Tennant', $whovian->say());
        }
        // 确认认同另一个实例最喜欢的医生
        public function testRespondToInAgreement()
        {
          $whovian = new Whovian('David Tennant');
          $opinion = 'David Tennant is the best doctor, period';
          $this->assertEquals('I agree!', $whovian->respondTo($opinion));
        }
        // 反对另一个实例最喜欢的医生
        public function testRespondToInDisagreement()
        {
          $whovian = new Whovian('David Tennant');
          $opinion = 'No way, Matt smith was awesome!';
          $whovian->respondTo($opinion);
        }
      }
    

终端执行测试

    vendor/bin/phpunit -c phpunit.xml
    

![](http://7xq3ry.com1.z0.glb.clouddn.com/phpunit-run) 测试的目录结构

    src/     
        /Whovian.php  
    tests/    
        /bootstrap.php  
        /WhovianTest.php
    vendor/
        /bin/  
        /composer/  
        /phpunit/  
        /autoload.php  
    composer.json  
    composer.lock  
    phpunit.xml