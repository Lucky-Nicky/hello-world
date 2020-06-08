import time
import unittest
from time import sleep

from parameterized import parameterized

from pages.AddUserpageFail import AdduserPageFail
from pages.LoginPage import LoginPage
from base.box import BoxDriver, GetExcel, GetPath, GetLogger
from pages.adduser_page import AdduserPage


class Test2(unittest.TestCase):

    get_path=GetPath()
    excel=GetExcel()
    success = excel.getexcel(get_path.get_file_path('selenium__0428/datas/information.xlsx'),'login_success')
    print(get_path.get_file_path('selenium__0428/datas/information.xlsx'))
    # success=excel.getexcel(r'D:\information.xlsx','login_success')
    fail=excel.getexcel(get_path.get_file_path('selenium__0428/datas/information.xlsx'),'login_fail')
    adduser=excel.getexcel(get_path.get_file_path('selenium__0428/datas/information.xlsx'),'adduser')
    adduserfail=excel.getexcel(get_path.get_file_path('selenium__0428/datas/information.xlsx'),'adduserfail')
    print(adduser)
    @classmethod
    def setUpClass(self) -> None:
        print('进入serUpClass')
        # d = BoxDriver('Chrome')
        # self.login=LoginPage()
        t=time.strftime('%Y-%m-%d_%H-%M-%S')
        self.get_log=GetLogger(GetPath().get_file_path('selenium__0428/logs/ranzhi%s.log'%t))
        d = BoxDriver('Chrome')
        self.login=LoginPage(d)
        sleep(2)


    @classmethod
    def tearDownClass(self) -> None:
        print('进入tearDown')
        self.login.base_driver.quit()
        # super().quit()


    def setUp(self) -> None:
        print('进入serUp')

    def tearDown(self) -> None:
        print('进入tearDown')

    @parameterized.expand(success)
    def test_login_success(self,name,pwd,realname,xuhao):
        # 成功用例
        try:
            self.login.login_page(name, pwd)
            username=self.login.text_name()
            # print(username)
            self.assertEqual(username,str(xuhao),'断言失败')

            print('username:%s,realname:%s'%(username,realname))
        except:
            raise Exception(self.get_log.error('登录成功，用例的第%s条断言失败，快去排查'%xuhao))
        finally:
            self.login.login_out()

    @parameterized.expand(fail)
    def test_login_fail(self,name,pwd,tips,xuhao):
        # 失败用例
        try:
            self.login.login_page(name,pwd)
            fact_duanyan = self.login.get_fail_tips()
            self.assertEqual(tips,fact_duanyan , '用户名和密码不正确也能登录成功？')
        except:
            raise Exception(self.get_log.error('登录失败用例的第%s条断言失败，快去排查'%xuhao))
        finally:
            # print('username:%s,realname:%s' % (username, realname))
            self.login.cancel()
    @parameterized.expand(adduserfail)
    def test_adduser(self,username, realname,  email, pwd1, pwd2):
        adduser=AdduserPageFail(BoxDriver('Chrome'))
        adduser.add_user_fail(username, realname,  email, pwd1, pwd2)

if __name__ == '__main__':
    unittest.main()
    # excel=GetExcel
    # E=excel.getexcel(r'D:\information.xlsx','Sheet1')
    # print(E)
    # path=GetPath()
    # path.get_file_path('/selenium__0428/datas/information.xlsx')
