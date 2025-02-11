机器人IO
============

.. toctree:: 
    :maxdepth: 5

设置控制箱数字量输出
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  设置控制箱数字量输出
    * @param  [in] id  io编号，范围[0~15]
    * @param  [in] status 0-关，1-开
    * @param  [in] smooth 0-不平滑， 1-平滑
    * @param  [in] block  0-阻塞，1-非阻塞
    * @return  错误码
    */
    errno_t  SetDO(int id, uint8_t status, uint8_t smooth, uint8_t block);

设置工具数字量输出
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  设置工具数字量输出
    * @param  [in] id  io编号，范围[0~1]
    * @param  [in] status 0-关，1-开
    * @param  [in] smooth 0-不平滑， 1-平滑
    * @param  [in] block  0-阻塞，1-非阻塞
    * @return  错误码
    */
    errno_t  SetToolDO(int id, uint8_t status, uint8_t smooth, uint8_t block);

设置控制箱模拟量输出
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  设置控制箱模拟量输出
    * @param  [in] id  io编号，范围[0~1]
    * @param  [in] value 电流或电压值百分比，范围[0~100]对应电流值[0~20mA]或电压[0~10V]
    * @param  [in] block  0-阻塞，1-非阻塞
    * @return  错误码
    */
    errno_t  SetAO(int id, float value, uint8_t block);

设置工具模拟量输出
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  设置工具模拟量输出
    * @param  [in] id  io编号，范围[0]
    * @param  [in] value 电流或电压值百分比，范围[0~100]对应电流值[0~20mA]或电压[0~10V]
    * @param  [in] block  0-阻塞，1-非阻塞
    * @return  错误码
    */
    errno_t  SetToolAO(int id, float value, uint8_t block);

获取控制箱数字量输入
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取控制箱数字量输入
    * @param  [in] id  io编号，范围[0~15]
    * @param  [in] block  0-阻塞，1-非阻塞
    * @param  [out] result  0-低电平，1-高电平
    * @return  错误码
    */   
    errno_t  GetDI(int id, uint8_t block, uint8_t *result);

获取工具数字量输入
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取工具数字量输入
    * @param  [in] id  io编号，范围[0~1]
    * @param  [in] block  0-阻塞，1-非阻塞
    * @param  [out] result  0-低电平，1-高电平
    * @return  错误码
    */   
    errno_t  GetToolDI(int id, uint8_t block, uint8_t *result);

等待控制箱数字量输入
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief 等待控制箱数字量输入
    * @param  [in] id  io编号，范围[0~15]
    * @param  [in]  status 0-关，1-开
    * @param  [in]  max_time  最大等待时间，单位ms
    * @param  [in]  opt  超时后策略，0-程序停止并提示超时，1-忽略超时提示程序继续执行，2-一直等待
    * @return  错误码
    */
    errno_t  WaitDI(int id, uint8_t status, int max_time, int opt);

等待控制箱多路数字量输入
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief 等待控制箱多路数字量输入
    * @param  [in] mode 0-多路与，1-多路或
    * @param  [in] id  io编号，bit0~bit7对应DI0~DI7，bit8~bit15对应CI0~CI7
    * @param  [in]  status 0-关，1-开
    * @param  [in]  max_time  最大等待时间，单位ms
    * @param  [in]  opt  超时后策略，0-程序停止并提示超时，1-忽略超时提示程序继续执行，2-一直等待
    * @return  错误码
    */
    errno_t  WaitMultiDI(int mode, int id, uint8_t status, int max_time, int opt);

等待工具数字量输入
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief 等待工具数字量输入
    * @param  [in] id  io编号，范围[0~1]
    * @param  [in]  status 0-关，1-开
    * @param  [in]  max_time  最大等待时间，单位ms
    * @param  [in]  opt  超时后策略，0-程序停止并提示超时，1-忽略超时提示程序继续执行，2-一直等待
    * @return  错误码
    */
    errno_t  WaitToolDI(int id, uint8_t status, int max_time, int opt);

获取控制箱模拟量输入
+++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取控制箱模拟量输入
    * @param  [in] id  io编号，范围[0~1]
    * @param  [in] block  0-阻塞，1-非阻塞
    * @param  [out] result  输入电流或电压值百分比，范围[0~100]对应电流值[0~20mS]或电压[0~10V]
    * @return  错误码
    */   
    errno_t  GetAI(int id, uint8_t block, float *result); 

获取工具模拟量输入
+++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取工具模拟量输入
    * @param  [in] id  io编号，范围[0]
    * @param  [in] block  0-阻塞，1-非阻塞
    * @param  [out] result  输入电流或电压值百分比，范围[0~100]对应电流值[0~20mS]或电压[0~10V]
    * @return  错误码
    */   
    errno_t  GetToolAI(int id, uint8_t block, float *result);

获取机器人末端点记录按钮状态
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取机器人末端点记录按钮状态
     * @param [out] state 按钮状态，0-按下，1-松开
     * @return 错误码
     */
    errno_t  GetAxlePointRecordBtnState(uint8_t *state);

获取机器人末端DO输出状态
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取机器人末端DO输出状态
     * @param [out] do_state DO输出状态，do0~do1对应bit1~bit2,从bit0开始
     * @return 错误码
     */
    errno_t  GetToolDO(uint8_t *do_state);

获取机器人控制器DO输出状态
++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取机器人控制器DO输出状态
     * @param [out] do_state_h DO输出状态，co0~co7对应bit0~bit7
     * @param [out] do_state_l DO输出状态，do0~do7对应bit0~bit7
     * @return 错误码
     */
    errno_t  GetDO(uint8_t *do_state_h, uint8_t *do_state_l);

等待控制箱模拟量输入
+++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief 等待控制箱模拟量输入
    * @param  [in] id  io编号，范围[0~1]
    * @param  [in]  sign 0-大于，1-小于
    * @param  [in]  value 输入电流或电压值百分比，范围[0~100]对应电流值[0~20mS]或电压[0~10V]
    * @param  [in]  max_time  最大等待时间，单位ms
    * @param  [in]  opt  超时后策略，0-程序停止并提示超时，1-忽略超时提示程序继续执行，2-一直等待
    * @return  错误码
    */
    errno_t  WaitAI(int id, int sign, float value, int max_time, int opt);  

等待工具模拟量输入
+++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief 等待工具模拟量输入
    * @param  [in] id  io编号，范围[0]
    * @param  [in]  sign 0-大于，1-小于
    * @param  [in]  value 输入电流或电压值百分比，范围[0~100]对应电流值[0~20mS]或电压[0~10V]
    * @param  [in]  max_time  最大等待时间，单位ms
    * @param  [in]  opt  超时后策略，0-程序停止并提示超时，1-忽略超时提示程序继续执行，2-一直等待
    * @return  错误码
    */
    errno_t  WaitToolAI(int id, int sign, float value, int max_time, int opt); 

代码示例
++++++++++++++

.. versionchanged:: C++SDK-v2.1.2.0
    
.. code-block:: c++
    :linenos:

    #include "libfairino/robot.h"

    //如果使用Windows，包含下面的头文件
    #include <string.h>
    #include <windows.h>
    //如果使用linux，包含下面的头文件
    /*
    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    */
    #include <chrono>
    #include <thread>

    using namespace std;
    int main(void)
    {
        FRRobot robot; 
        robot.RPC("192.168.58.2"); 

        uint8_t status = 1;
        uint8_t smooth = 0;
        uint8_t block  = 0;
        uint8_t di = 0, tool_di = 0;
        float ai = 0.0, tool_ai = 0.0;
        float value = 0.0;
        int i;

        for(i = 0; i < 16; i++)
        {
            robot.SetDO(i, status, smooth, block);
            robot.WaitMs(1000);
        }

        status = 0;

        for(i = 0; i < 16; i++)
        {
            robot.SetDO(i, status, smooth, block);
            robot.WaitMs(1000);
        }

        status = 1;

        for(i = 0; i < 2; i++)
        {
            robot.SetToolDO(i, status, smooth, block);
            robot.WaitMs(1000);
        }

        status = 0;

        for(i = 0; i < 2; i++)
        {
            robot.SetToolDO(i, status, smooth, block);
            robot.WaitMs(1000);
        }

        value = 50.0;
        robot.SetAO(0, value, block);
        value = 100.0;
        robot.SetAO(1, value, block);
        robot.WaitMs(1000);
        value = 0.0;
        robot.SetAO(0, value, block);
        value = 0.0;
        robot.SetAO(1, value, block);

        value = 100.0;
        robot.SetToolAO(0, value, block);
        robot.WaitMs(1000);
        value = 0.0;
        robot.SetToolAO(0, value, block);

        robot.GetDI(0, block, &di);
        printf("di0:%u\n", di);
        robot.WaitDI(0,1,0,2);              //一直等待
        robot.WaitMultiDI(1,3,3,10000,2);   //一直等待
        tool_di = robot.GetToolDI(1, block, &tool_di);
        printf("tool_di1:%u\n", tool_di);
        robot.WaitToolDI(1,1,0,2);          //一直等待

        robot.GetAI(0,block, &ai);
        printf("ai0:%f\n", ai);
        robot.WaitAI(0,0,50,0,2);           //一直等待
        robot.WaitToolAI(0,0,50,0,2);       //一直等待
        tool_ai = robot.GetToolAI(0,block, &tool_ai);
        printf("tool_ai0:%f\n", tool_ai);

        uint8_t _button_state = 0;
        robot.GetAxlePointRecordBtnState(&_button_state);
        printf("_button_state is: %u\n", _button_state);

        uint8_t tool_do_state = 0;
        robot.GetToolDO(&tool_do_state);
        printf("tool DO state is: %u\n", tool_do_state);

        uint8_t do_state_h = 0;
        uint8_t do_state_l = 0;
        robot.GetDO(&do_state_h, &do_state_l);
        printf("DO state high is: %u \n DO state low is: %u\n", do_state_h, do_state_l);
        return 0;
    }

获取机器人软件版本
+++++++++++++++++++++++++

.. versionadded:: C++SDK-v2.1.1.0

.. code-block:: c++
    :linenos:

    /**
    * @brief 获取机器人软件版本
    * @param[out]	robotModel 机器人型号
    * @param[out]	webversion web版本
    * @param[out]	controllerVersion 控制器版本
    * @return 错误码
    */
    errno_t GetSoftwareVersion(char robotModel[64], char webVersion[64], char controllerVersion[64]);

获取机器人硬件版本
+++++++++++++++++++++++++

.. versionadded:: C++SDK-v2.1.1.0

.. code-block:: c++
    :linenos:

    /**
    * @brief 获取机器人硬件版本
    * @param[out] ctrlBoxBoardversion 控制箱载板硬件版本
    * @param[out] driver1version 驱动器1硬件版本
    * @param[out] driver2version 驱动器2硬件版本
    * @param[out] driver3version 驱动器3硬件版本
    * @param[out] driver4version 驱动器4硬件版本
    * @param[out] driver5version 驱动器5硬件版本
    * @param[out] driver6version 驱动器6硬件版本
    * @param[out] endBoardversion 未端版硬件版本
    */
    errno_t GetHardwareVersion(char ctrlBoxBoardversion[128], char driver1version[128], char driver2version[128], char driver3version[128], char driver4version[128], char driver5version[128], char driver6version[128], char endBoardversion[128]);

获取机器人固件版本
+++++++++++++++++++++++++

.. versionadded:: C++SDK-v2.1.1.0

.. code-block:: c++
    :linenos:

    /**
    * @brief 获取机器人固件版本
    * @param[out] ctrlBoxBoardversion 控制箱载板固件版本
    * @param[out] driver1version 驱动器1固件版本
    * @param[out] driver2version 驱动器2固件版本
    * @param[out] driver3version 驱动器3固件版本
    * @param[out] driver4version 驱动器4固件版本
    * @param[out] driver5version 驱动器5固件版本
    * @param[out] driver6version 驱动器6固件版本
    * @param[out] endBoardversion 未端版固件版本
    */
    errno_t GetFirmwareVersion(char ctrlBoxBoardversion[128], char driver1version[128], char driver2version[128], char driver3version[128], char driver4version[128], char driver5version[128], char driver6version[128], char endBoardversion[128]);

代码示例
+++++++++++++++++++++++++
.. versionadded:: C++SDK-v2.1.2.0

.. code-block:: c++
    :linenos:

    #include "libfairino/robot.h"

    //如果使用Windows，包含下面的头文件
    #include <string.h>
    #include <windows.h>
    //如果使用linux，包含下面的头文件
    /*
    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    */
    #include <chrono>
    #include <thread>

    using namespace std;
    int main(void)
    {
        FRRobot robot; 
        robot.RPC("192.168.58.2"); 

        int retval = 0;
        char robotModel[64] = {0};
        char webversion[64] = {0};
        char controllerVersion[64] = {0};

        char ctrlBoxBoardversion[128] = {0};
        char driver1version[128] = {0};
        char driver2version[128] = {0};
        char driver3version[128] = {0};
        char driver4version[128] = {0};
        char driver5version[128] = {0};
        char driver6version[128] = {0};
        char endBoardversion[128] = {0};

        retval = robot.GetSoftwareVersion(robotModel, webversion, controllerVersion);
        printf("Getsoftwareversion retval is: %d\n", retval);
        printf("robotmodel is: %s, webversion is: %s, controllerVersion is: %s \n", robotModel, webversion, controllerVersion);

        retval = robot.GetHardwareVersion(ctrlBoxBoardversion,  driver1version,  driver2version, driver3version,  driver4version,  driver5version, driver6version,  endBoardversion);
        printf("GetHardwareversion retval is: %d\n", retval);
        printf("GetHardwareversion get hardware versoin is: %s, %s, %s, %s, %s, %s, %s, %s\n", ctrlBoxBoardversion, driver1version, driver2version, driver3version, driver4version, driver5version, driver6version, endBoardversion);

        retval = robot.GetFirmwareVersion(ctrlBoxBoardversion,  driver1version,  driver2version, driver3version,  driver4version,  driver5version, driver6version, endBoardversion);
        printf("GetFirmwareversion retval is: %d\n", retval);
        printf("GetHardwareversion get hardware versoin is: %s, %s, %s, %s, %s, %s, %s, %s\n", ctrlBoxBoardversion, driver1version, driver2version, driver3version, driver4version, driver5version, driver6version, endBoardversion);

        return 0;
    }
