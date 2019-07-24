---
title: unittest单元测试
tags:
  - python
url: 118.html
id: 118
comments: false
categories:
  - 后端
date: 2016-11-30 11:13:19
---

#### unittest基础使用方法

    # -*- coding:utf-8 -*-
    # calculator.py
    # 需要测试的类
    class Count:
    
        def __init__(self, a, b):
            self.a = int(a)
            self.b = int(b)
    
        def add(self):
            return self.a + self.b
    
    

    # -*- coding: utf-8 -*-
    # test.py 测试类
    from calculator import Count
    import unittest
    class TestCount(unittest.TestCase):
        def setUp(self):
            print 'test start'
    
        def test_add(self):
            j = Count(2,3)
            self.assertEqual(j.add(), 5)
    
        def test_add2(self):
            j = Count(41, 76)
            self.assertEqual(j.add(), 115, 'not equal')
    
        def tearDown(self):
            print 'test end'
    
    
    if __name__ == '__main__':
        # 测试全部方法
        # unittest.main()
        # 构造测试集
        suite = unittest.TestSuite()
        # 指定测试方法
        suite.addTest(TestCount('test_add2'))
        # 执行测试
        runner = unittest.TextTestRunner()
        runner.run(suite)
    
    

#### 自动识别测试用例

    # -*- coding:utf-8 -*-
    # calculator.py
    # 需要测试的类
    class Count:
    
        def __init__(self, a, b):
            self.a = int(a)
            self.b = int(b)
    
        def add(self):
            return self.a + self.b
    
        def sub(self):
            return self.a - self.b
    

    # -*- coding: utf-8 -*-
    # testadd.py 测试类
    from calculator import Count
    import unittest
    class TestAdd(unittest.TestCase):
        def setUp(self):
            print 'test add start'
    
        def test_add(self):
            j = Count(2,3)
            self.assertEqual(j.add(), 5)
    
        def test_add2(self):
            j = Count(41, 76)
            self.assertEqual(j.add(), 115, 'not equal')
    
        def tearDown(self):
            print 'test add end'
    
    
    if __name__ == '__main__':
        # 测试全部方法
        unittest.main()
    

    # -*- coding: utf-8 -*-
    # testsub.py 测试类
    from calculator import Count
    import unittest
    
    
    class TestSub(unittest.TestCase):
        def setUp(self):
            print 'test sub start'
    
        def test_sub(self):
            j = Count(2,3)
            self.assertEqual(j.sub(), 5)
    
        def test_sub2(self):
            j = Count(41, 76)
            self.assertEqual(j.sub(), 115, 'not equal')
    
        def tearDown(self):
            print 'test sub end'
    
    
    if __name__ == '__main__':
        # 测试全部方法
        unittest.main()
    

    # -*- coding: utf-8 -*-
    # runtest.py
    import unittest
    test_dir = './'
    discover = unittest.defaultTestLoader.discover(test_dir, pattern='test*.py')
    if __name__ == '__main__':
        runner = unittest.TextTestRunner()
        runner.run(discover)
    

#### 断言方法啊

    # unittest.TestCase.assertEqual(a, b, msg=None)   a == b
    # unittest.TestCase.assertNotEqual(a, b) a !=b  
    # unittest.TestCase.assertTrue(x)   x is True
    # unittest.TestCase.assertFalse(x)  x is False
    # unittest.TestCase.assertIs(a, b)  x is b
    # unittest.TestCase.assertIsNot(a, b)  a is not b
    # unittest.TestCase.assertIsNotNone(x)  x is not None
    # unittest.TestCase.assertIn(a, b)  a in b
    # unittest.TestCase.assertNotIn(a, b) a not in b
    # unittest.TestCase.assertNotIsInstance(a, b) isinstance(a,b)
    # unittest.TestCase.assertNotIsInstance(a, b) not isinstance(a,b)
    

#### 跳过测试与预期失败

    # -*- coding: utf-8 -*-
    # test.py
    import unittest
    
    class MyTest(unittest.TestCase):
    
        def setUp(self):
            pass
        def tearDown(self):
            pass
    
        @unittest.skip(u'直接跳过测试')
        def test_skip(self):
            print 'test aaa'
    
        @unittest.skipIf(3 > 2, u'条件满足时跳过测试')
        def test_skip_if(self):
            print 'test bbb'
    
        @unittest.skipUnless(3 > 2, u'条件满足时执行测试')
        def test_skip_unless(self):
            print 'test ccc'
    
        # 不管测试成功与否，都标记为失败
        @unittest.expectedFailure
        def test_expected_failure(self):
            print 'test ddd'
            self.assertEqual(2, 3)
    
    if __name__ == '__main__':
        unittest.main()