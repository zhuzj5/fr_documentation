机器人力控
============

.. toctree:: 
    :maxdepth: 5

力传感器配置
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  配置力传感器
    * @param  [in] company  力传感器厂商，17-坤维科技
    * @param  [in] device  设备号，暂不使用，默认为0
    * @param  [in] softvesion  软件版本号，暂不使用，默认为0
    * @param  [in] bus 设备挂在末端总线位置，暂不使用，默认为0
    * @return  错误码
    */
    errno_t  FT_SetConfig(int company, int device, int softvesion, int bus);

获取力传感器配置
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取力传感器配置
    * @param  [in] company  力传感器厂商，待定
    * @param  [in] device  设备号，暂不使用，默认为0
    * @param  [in] softvesion  软件版本号，暂不使用，默认为0
    * @param  [in] bus 设备挂在末端总线位置，暂不使用，默认为0
    * @return  错误码
    */
    errno_t  FT_GetConfig(int *company, int *device, int *softvesion, int *bus);

力传感器激活
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  力传感器激活
    * @param  [in] act  0-复位，1-激活
    * @return  错误码
    */
    errno_t  FT_Activate(uint8_t act);

力传感器校零
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  力传感器校零
    * @param  [in] act  0-去除零点，1-零点矫正
    * @return  错误码
    */
    errno_t  FT_SetZero(uint8_t act);   

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>

    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        int company = 17;
        int device = 0;
        int softversion = 0;
        int bus = 1;
        int index = 1;
        int act = 0;

        robot.FT_SetConfig(company, device, softversion, bus);
        sleep(1);
        robot.FT_GetConfig(&company, &device, &softversion, &bus);
        printf("FT config:%d,%d,%d,%d\n", company, device, softversion, bus);
        sleep(1);

        robot.FT_Activate(act);
        sleep(1);
        act = 1;
        robot.FT_Activate(act);
        sleep(1);

        robot.SetLoadWeight(0.0);
        sleep(1);
        DescTran coord;
        memset(&coord, 0, sizeof(DescTran));
        robot.SetLoadCoord(&coord);
        sleep(1);
        robot.FT_SetZero(0);
        sleep(1);

        ForceTorque ft;
        memset(&ft, 0, sizeof(ForceTorque));
        robot.FT_GetForceTorqueOrigin(&ft);
        printf("ft origin:%f,%f,%f,%f,%f,%f\n", ft.fx,ft.fy,ft.fz,ft.tx,ft.ty,ft.tz);
        robot.FT_SetZero(1);
        sleep(1);
        memset(&ft, 0, sizeof(ForceTorque));
        printf("ft rcs:%f,%f,%f,%f,%f,%f\n",ft.fx,ft.fy,ft.fz,ft.tx,ft.ty,ft.tz);

        return 0;
    }

设置力传感器参考坐标系
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  设置力传感器参考坐标系
    * @param  [in] ref  0-工具坐标系，1-基坐标系
    * @return  错误码
    */
    errno_t  FT_SetRCS(uint8_t ref); 

负载重量辨识记录
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  负载重量辨识记录
    * @param  [in] id  传感器坐标系编号，范围[1~14]
    * @return  错误码
    */
    errno_t  FT_PdIdenRecord(int id);   

负载重量辨识计算
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  负载重量辨识计算
    * @param  [out] weight  负载重量，单位kg
    * @return  错误码
    */   
    errno_t  FT_PdIdenCompute(float *weight);

负载质心辨识记录
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  负载质心辨识记录
    * @param  [in] id  传感器坐标系编号，范围[1~14]
    * @param  [in] index 点编号，范围[1~3]
    * @return  错误码
    */
    errno_t  FT_PdCogIdenRecord(int id, int index);    

负载质心辨识计算
+++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  负载质心辨识计算
    * @param  [out] cog  负载质心，单位mm
    * @return  错误码
    */   
    errno_t  FT_PdCogIdenCompute(DescTran *cog); 

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        float weight;

        DescPose tcoord, desc_p1, desc_p2, desc_p3;
        memset(&tcoord, 0, sizeof(DescPose));
        memset(&desc_p1, 0, sizeof(DescPose));
        memset(&desc_p2, 0, sizeof(DescPose));
        memset(&desc_p3, 0, sizeof(DescPose));

        robot.FT_SetRCS(0);
        sleep(1);

        tcoord.tran.z = 35.0;
        robot.SetToolCoord(10, &tcoord, 1, 0);
        sleep(1);
        robot.FT_PdIdenRecord(10);
        sleep(1);
        robot.FT_PdIdenCompute(&weight);
        printf("payload weight:%f\n", weight);

        desc_p1.tran.x = -160.619;
        desc_p1.tran.y = -586.138;
        desc_p1.tran.z = 384.988;
        desc_p1.rpy.rx = -170.166;
        desc_p1.rpy.ry = -44.782;
        desc_p1.rpy.rz = 169.295;

        desc_p2.tran.x = -87.615;
        desc_p2.tran.y = -606.209;
        desc_p2.tran.z = 556.119;
        desc_p2.rpy.rx = -102.495;
        desc_p2.rpy.ry = 10.118;
        desc_p2.rpy.rz = 178.985;

        desc_p3.tran.x = 41.479;
        desc_p3.tran.y = -557.243;
        desc_p3.tran.z = 484.407;
        desc_p3.rpy.rx = -125.174;
        desc_p3.rpy.ry = 46.995;
        desc_p3.rpy.rz = -132.165;

        robot.MoveCart(&desc_p1, 9, 0, 100.0, 100.0, 100.0, -1.0, -1);
        sleep(1);
        robot.FT_PdCogIdenRecord(10, 1);
        robot.MoveCart(&desc_p2, 9, 0, 100.0, 100.0, 100.0, -1.0, -1);
        sleep(1);
        robot.FT_PdCogIdenRecord(10, 2);
        robot.MoveCart(&desc_p3, 9, 0, 100.0, 100.0, 100.0, -1.0, -1);
        sleep(1);
        robot.FT_PdCogIdenRecord(10, 3);
        sleep(1);
        DescTran cog;
        memset(&cog, 0, sizeof(DescTran));
        robot.FT_PdCogIdenCompute(&cog);
        printf("cog:%f,%f,%f\n",cog.x, cog.y, cog.z);

        return 0;
    }

获取参考坐标系下力/扭矩数据
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取参考坐标系下力/扭矩数据
    * @param  [out] ft  力/扭矩，fx,fy,fz,tx,ty,tz
    * @return  错误码
    */   
    errno_t  FT_GetForceTorqueRCS(ForceTorque *ft); 

获取力传感器原始力/扭矩数据
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  获取力传感器原始力/扭矩数据
    * @param  [out] ft  力/扭矩，fx,fy,fz,tx,ty,tz
    * @return  错误码
    */   
    errno_t  FT_GetForceTorqueOrigin(ForceTorque *ft); 

碰撞守护
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  碰撞守护
    * @param  [in] flag 0-关闭碰撞守护，1-开启碰撞守护
    * @param  [in] sensor_id 力传感器编号
    * @param  [in] select  选择六个自由度是否检测碰撞，0-不检测，1-检测
    * @param  [in] ft  碰撞力/扭矩，fx,fy,fz,tx,ty,tz
    * @param  [in] max_threshold 最大阈值
    * @param  [in] min_threshold 最小阈值
    * @note   力/扭矩检测范围：(ft-min_threshold, ft+max_threshold)
    * @return  错误码
    */   
    errno_t  FT_Guard(uint8_t flag, int sensor_id, uint8_t select[6], ForceTorque *ft, float max_threshold[6], float min_threshold[6]); 

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        uint8_t flag = 1;
        uint8_t sensor_id = 1;
        uint8_t select[6] = {1,1,1,1,1,1};
        float max_threshold[6] = {10.0,10.0,10.0,10.0,10.0,10.0};
        float min_threshold[6] = {5.0,5.0,5.0,5.0,5.0,5.0};

        ForceTorque ft;
        DescPose desc_p1, desc_p2, desc_p3;
        memset(&ft, 0, sizeof(ForceTorque));
        memset(&desc_p1, 0, sizeof(DescPose));
        memset(&desc_p2, 0, sizeof(DescPose));
        memset(&desc_p3, 0, sizeof(DescPose));

        desc_p1.tran.x = -160.619;
        desc_p1.tran.y = -586.138;
        desc_p1.tran.z = 384.988;
        desc_p1.rpy.rx = -170.166;
        desc_p1.rpy.ry = -44.782;
        desc_p1.rpy.rz = 169.295;

        desc_p2.tran.x = -87.615;
        desc_p2.tran.y = -606.209;
        desc_p2.tran.z = 556.119;
        desc_p2.rpy.rx = -102.495;
        desc_p2.rpy.ry = 10.118;
        desc_p2.rpy.rz = 178.985;

        desc_p3.tran.x = 41.479;
        desc_p3.tran.y = -557.243;
        desc_p3.tran.z = 484.407;
        desc_p3.rpy.rx = -125.174;
        desc_p3.rpy.ry = 46.995;
        desc_p3.rpy.rz = -132.165;

        robot.FT_Guard(flag, sensor_id, select, &ft, max_threshold, min_threshold);
        robot.MoveCart(&desc_p1,9,0,100.0,100.0,100.0,-1.0,-1);
        robot.MoveCart(&desc_p2,9,0,100.0,100.0,100.0,-1.0,-1);
        robot.MoveCart(&desc_p3,9,0,100.0,100.0,100.0,-1.0,-1);
        flag = 0;
        robot.FT_Guard(flag, sensor_id, select, &ft, max_threshold, min_threshold);

        return 0;
    }

恒力控制
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  恒力控制
    * @param  [in] flag 0-关闭恒力控制，1-开启恒力控制
    * @param  [in] sensor_id 力传感器编号
    * @param  [in] select  选择六个自由度是否检测碰撞，0-不检测，1-检测
    * @param  [in] ft  碰撞力/扭矩，fx,fy,fz,tx,ty,tz
    * @param  [in] ft_pid 力pid参数，力矩pid参数
    * @param  [in] adj_sign 自适应启停控制，0-关闭，1-开启
    * @param  [in] ILC_sign ILC启停控制， 0-停止，1-训练，2-实操
    * @param  [in] 最大调整距离，单位mm
    * @param  [in] 最大调整角度，单位deg
    * @return  错误码
    */   
    errno_t  FT_Control(uint8_t flag, int sensor_id, uint8_t select[6], ForceTorque *ft, float ft_pid[6], uint8_t adj_sign, uint8_t ILC_sign, float max_dis, float max_ang);   

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        uint8_t flag = 1;
        uint8_t sensor_id = 1;
        uint8_t select[6] = {0,0,1,0,0,0};
        float ft_pid[6] = {0.0005,0.0,0.0,0.0,0.0,0.0};
        uint8_t adj_sign = 0;
        uint8_t ILC_sign = 0;
        float max_dis = 100.0;
        float max_ang = 0.0;

        ForceTorque ft;
        DescPose desc_p1, desc_p2, offset_pos;
        JointPos j1,j2;
        ExaxisPos epos;
        memset(&ft, 0, sizeof(ForceTorque));
        memset(&desc_p1, 0, sizeof(DescPose));
        memset(&desc_p2, 0, sizeof(DescPose));
        memset(&offset_pos, 0, sizeof(DescPose));
        memset(&epos, 0, sizeof(ExaxisPos));
        memset(&j1, 0, sizeof(JointPos));
        memset(&j2, 0, sizeof(JointPos));

        j1 = {-68.987,-96.414,-111.45,-61.105,92.884,11.089};
        j2 = {-107.596,-109.154,-104.735,-56.176,90.739,11.091};

        desc_p1.tran.x = 62.795;
        desc_p1.tran.y = -511.979;
        desc_p1.tran.z = 291.697;
        desc_p1.rpy.rx = -179.545;
        desc_p1.rpy.ry = 3.027;
        desc_p1.rpy.rz = -170.039;

        desc_p2.tran.x = -294.768;
        desc_p2.tran.y = -503.708;
        desc_p2.tran.z = 233.158;
        desc_p2.rpy.rx = 179.799;
        desc_p2.rpy.ry = 0.713;
        desc_p2.rpy.rz = 151.309;

        ft.fz = -10.0;

        robot.MoveJ(&j1,&desc_p1,9,0,100.0,180.0,100.0,&epos,-1.0,0,&offset_pos);
        robot.FT_Control(flag, sensor_id, select, &ft, ft_pid, adj_sign, ILC_sign, max_dis, max_ang);
        robot.MoveL(&j2,&desc_p2,9,0,100.0,180.0,20.0,-1.0,&epos,0,0,&offset_pos);
        flag = 0;
        robot.FT_Control(flag, sensor_id, select, &ft, ft_pid, adj_sign, ILC_sign, max_dis, max_ang);

        return 0;
    }

螺旋线探索
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  螺旋线探索
    * @param  [in] rcs 参考坐标系，0-工具坐标系，1-基坐标系
    * @param  [in] dr 每圈半径进给量
    * @param  [in] ft 力/扭矩阈值，fx,fy,fz,tx,ty,tz，范围[0~100]
    * @param  [in] max_t_ms 最大探索时间，单位ms
    * @param  [in] max_vel 最大线速度，单位mm/s
    * @return  错误码
    */   
    errno_t  FT_SpiralSearch(int rcs, float dr, float ft, float max_t_ms, float max_vel);  

旋转插入
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  旋转插入
    * @param  [in] rcs 参考坐标系，0-工具坐标系，1-基坐标系
    * @param  [in] angVelRot 旋转角速度，单位deg/s
    * @param  [in] ft  力/扭矩阈值，fx,fy,fz,tx,ty,tz，范围[0~100]
    * @param  [in] max_angle 最大旋转角度，单位deg
    * @param  [in] orn 力/扭矩方向，1-沿z轴方向，2-绕z轴方向
    * @param  [in] max_angAcc 最大旋转加速度，单位deg/s^2，暂不使用，默认为0
    * @param  [in] rotorn  旋转方向，1-顺时针，2-逆时针
    * @return  错误码
    */   
    errno_t  FT_RotInsertion(int rcs, float angVelRot, float ft, float max_angle, uint8_t orn, float max_angAcc, uint8_t rotorn);    

直线插入
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  直线插入
    * @param  [in] rcs 参考坐标系，0-工具坐标系，1-基坐标系
    * @param  [in] ft  力/扭矩阈值，fx,fy,fz,tx,ty,tz，范围[0~100]
    * @param  [in] lin_v 直线速度，单位mm/s
    * @param  [in] lin_a 直线加速度，单位mm/s^2，暂不使用
    * @param  [in] max_dis 最大插入距离，单位mm
    * @param  [in] linorn  插入方向，0-负方向，1-正方向
    * @return  错误码
    */   
    errno_t  FT_LinInsertion(int rcs, float ft, float lin_v, float lin_a, float max_dis, uint8_t linorn);    

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        //恒力参数
        uint8_t status = 1;  //恒力控制开启标志，0-关，1-开
        int sensor_num = 1; //力传感器编号
        float gain[6] = {0.0001,0.0,0.0,0.0,0.0,0.0};  //最大阈值
        uint8_t adj_sign = 0;  //自适应启停状态，0-关闭，1-开启
        uint8_t ILC_sign = 0;  //ILC控制启停状态，0-停止，1-训练，2-实操
        float max_dis = 100.0;  //最大调整距离
        float max_ang = 5.0;  //最大调整角度

        ForceTorque ft;
        memset(&ft, 0, sizeof(ForceTorque));

        //螺旋线探索参数
        int rcs = 0;  //参考坐标系，0-工具坐标系，1-基坐标系
        float dr = 0.7;  //每圈半径进给量，单位mm
        float fFinish = 1.0; //力或力矩阈值（0~100），单位N或Nm
        float t = 60000.0; //最大探索时间，单位ms
        float vmax = 3.0; //线速度最大值，单位mm/s

        //直线插入参数
        float force_goal = 20.0;  //力或力矩阈值（0~100），单位N或Nm
        float lin_v = 0.0; //直线速度，单位mm/s
        float lin_a = 0.0; //直线加速度，单位mm/s^2,暂不使用
        float disMax = 100.0; //最大插入距离，单位mm
        uint8_t linorn = 1; //插入方向，1-正方向，2-负方向

        //旋转插入参数
        float angVelRot = 2.0;  //旋转角速度，单位°/s
        float forceInsertion = 1.0; //力或力矩阈值（0~100），单位N或Nm
        int angleMax= 45; //最大旋转角度，单位°
        uint8_t orn = 1; //力的方向，1-fz,2-mz
        float angAccmax = 0.0; //最大旋转角加速度，单位°/s^2,暂不使用
        uint8_t rotorn = 1; //旋转方向，1-顺时针，2-逆时针

        uint8_t select1[6] = {0,0,1,1,1,0}; //六个自由度选择[fx,fy,fz,mx,my,mz]，0-不生效，1-生效
        ft.fz = -10.0;
        robot.FT_Control(status,sensor_num,select1,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);
        robot.FT_SpiralSearch(rcs,dr,fFinish,t,vmax);
        status = 0;
        robot.FT_Control(status,sensor_num,select1,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);

        uint8_t select2[6] = {1,1,1,0,0,0};  //六个自由度选择[fx,fy,fz,mx,my,mz]，0-不生效，1-生效
        gain[0] = 0.00005;
        ft.fz = -30.0;
        status = 1;
        robot.FT_Control(status,sensor_num,select2,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);
        robot.FT_LinInsertion(rcs,force_goal,lin_v,lin_a,disMax,linorn);
        status = 0;
        robot.FT_Control(status,sensor_num,select2,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);

        uint8_t select3[6] = {0,0,1,1,1,0};  //六个自由度选择[fx,fy,fz,mx,my,mz]，0-不生效，1-生效
        ft.fz = -10.0;
        gain[0] = 0.0001;
        status = 1;
        robot.FT_Control(status,sensor_num,select3,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);
        robot.FT_RotInsertion(rcs,angVelRot,forceInsertion,angleMax,orn,angAccmax,rotorn);
        status = 0;
        robot.FT_Control(status,sensor_num,select3,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);

        uint8_t select4[6] = {1,1,1,0,0,0};  //六个自由度选择[fx,fy,fz,mx,my,mz]，0-不生效，1-生效
        ft.fz = -30.0;
        status = 1;
        robot.FT_Control(status,sensor_num,select4,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);
        robot.FT_LinInsertion(rcs,force_goal,lin_v,lin_a,disMax,linorn);
        status = 0;
        robot.FT_Control(status,sensor_num,select4,&ft,gain,adj_sign,ILC_sign,max_dis,max_ang);

        return 0;
    }

表面定位
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  表面定位
    * @param  [in] rcs 参考坐标系，0-工具坐标系，1-基坐标系
    * @param  [in] dir  移动方向，1-正方向，2-负方向 
    * @param  [in] axis 移动轴，1-x轴，2-y轴，3-z轴
    * @param  [in] lin_v 探索直线速度，单位mm/s
    * @param  [in] lin_a 探索直线加速度，单位mm/s^2，暂不使用，默认为0
    * @param  [in] max_dis 最大探索距离，单位mm
    * @param  [in] ft  动作终止力/扭矩阈值，fx,fy,fz,tx,ty,tz  
    * @return  错误码
    */   
    errno_t  FT_FindSurface(int rcs, uint8_t dir, uint8_t axis, float lin_v, float lin_a, float max_dis, float ft);   

计算中间平面位置开始
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  计算中间平面位置开始
    * @return  错误码
    */   
    errno_t  FT_CalCenterStart();

计算中间平面位置结束
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  计算中间平面位置结束
    * @param  [out] pos 中间平面位姿
    * @return  错误码
    */      
    errno_t  FT_CalCenterEnd(DescPose *pos);

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        int rcs = 0;
        uint8_t dir = 1;
        uint8_t axis = 1;
        float lin_v = 3.0;
        float lin_a = 0.0;
        float maxdis = 50.0;
        float ft_goal = 2.0;

        DescPose desc_pos, xcenter, ycenter;
        ForceTorque ft;
        memset(&desc_pos, 0, sizeof(DescPose));
        memset(&xcenter, 0, sizeof(DescPose));
        memset(&ycenter, 0, sizeof(DescPose));
        memset(&ft, 0, sizeof(ForceTorque));

        desc_pos.tran.x = -230.959;
        desc_pos.tran.y = -364.017;
        desc_pos.tran.z = 217.5;
        desc_pos.rpy.rx = -179.004;
        desc_pos.rpy.ry = 0.002;
        desc_pos.rpy.rz = 89.999;

        ft.fx = -2.0;

        robot.MoveCart(&desc_pos, 9,0,100.0,100.0,100.0,-1.0,-1);

        robot.FT_CalCenterStart();
        robot.FT_FindSurface(rcs, dir, axis, lin_v, lin_a, maxdis, ft_goal);
        robot.MoveCart(&desc_pos, 9,0,100.0,100.0,100.0,-1.0,-1);
        robot.WaitMs(1000);

        dir = 2;
        robot.FT_FindSurface(rcs, dir, axis, lin_v, lin_a, maxdis, ft_goal);
        robot.FT_CalCenterEnd(&xcenter);
        printf("xcenter:%f,%f,%f,%f,%f,%f\n",xcenter.tran.x,xcenter.tran.y,xcenter.tran.z,xcenter.rpy.rx,xcenter.rpy.ry,xcenter.rpy.rz);
        robot.MoveCart(&xcenter, 9,0,60.0,50.0,50.0,-1.0,-1);

        robot.FT_CalCenterStart();
        dir = 1;
        axis = 2;
        lin_v = 6.0;
        maxdis = 150.0;
        robot.FT_FindSurface(rcs, dir, axis, lin_v, lin_a, maxdis, ft_goal);
        robot.MoveCart(&desc_pos, 9,0,100.0,100.0,100.0,-1.0,-1);
        robot.WaitMs(1000);

        dir = 2;
        robot.FT_FindSurface(rcs, dir, axis, lin_v, lin_a, maxdis, ft_goal);  
        robot.FT_CalCenterEnd(&ycenter);
        printf("ycenter:%f,%f,%f,%f,%f,%f\n",ycenter.tran.x,ycenter.tran.y,ycenter.tran.z,ycenter.rpy.rx,ycenter.rpy.ry,ycenter.rpy.rz);
        robot.MoveCart(&ycenter, 9,0,60.0,50.0,50.0,0.0,-1);   

        return 0;
    }

柔顺控制开启
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  柔顺控制开启
    * @param  [in] p 位置调节系数或柔顺系数
    * @param  [in] force 柔顺开启力阈值，单位N
    * @return  错误码
    */   
    errno_t  FT_ComplianceStart(float p, float force); 

柔顺控制关闭
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
    * @brief  柔顺控制关闭
    * @return  错误码
    */   
    errno_t  FT_ComplianceStop(); 

负载辨识初始化
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 负载辨识初始化
     * @return 错误码
     */
    errno_t LoadIdentifyDynFilterInit();

负载辨识初始化
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 负载辨识初始化
     * @return 错误码
     */
    errno_t LoadIdentifyDynVarInit();

负载辨识主程序
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 负载辨识主程序
     * @param [in] joint_torque 关节扭矩
     * @param [in] joint_pos 关节位置
     * @param [in] t 采样周期
     * @return 错误码
     */
    errno_t LoadIdentifyMain(double joint_torque[6], double joint_pos[6], double t);

获取负载辨识结果
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取负载辨识结果
     * @param [in] gain  
     * @param [out] weight 负载重量
     * @param [out] cog 负载质心
     * @return 错误码
     */
    errno_t LoadIdentifyGetResult(double gain[12], double *weight, DescTran *cog);

传动带启动、停止
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 传动带启动、停止
     * @param [in] status 状态，1-启动，0-停止 
     * @return 错误码
     */
    errno_t ConveyorStartEnd(uint8_t status);

记录IO检测点
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 记录IO检测点
     * @return 错误码
     */
    errno_t ConveyorPointIORecord();

记录A点
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 记录A点
     * @return 错误码
     */
    errno_t ConveyorPointARecord();

记录参考点
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 记录参考点
     * @return 错误码
     */
    errno_t ConveyorRefPointRecord();

记录B点
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 记录B点
     * @return 错误码
     */
    errno_t ConveyorPointBRecord();

传送带工件IO检测
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 传送带工件IO检测
     * @param [in] max_t 最大检测时间，单位ms
     * @return 错误码
     */
    errno_t ConveyorIODetect(int max_t);

获取物体当前位置
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取物体当前位置
     * @param [in] mode 
     * @return 错误码
     */
    errno_t ConveyorGetTrackData(int mode);

传动带跟踪开始
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 传动带跟踪开始
     * @param [in] status 状态，1-启动，0-停止 
     * @return 错误码
     */
    errno_t ConveyorTrackStart(uint8_t status);

传动带跟踪停止
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 传动带跟踪停止
     * @return 错误码
     */
    errno_t ConveyorTrackEnd();

传动带参数配置
+++++++++++++++++++++++++++++++++++++++++++++
.. versionchanged:: C++SDK-v2.1.2.0

.. code-block:: c++
    :linenos:

	/**
	 * @brief 传动带参数配置
	 * @param [in] para[0] 编码器通道 1~2
	 * @param [in] para[1] 编码器转一圈的脉冲数
	 * @param [in] para[2] 编码器转一圈传送带行走距离
	 * @param [in] para[3] 工件坐标系编号 针对跟踪运动功能选择工件坐标系编号，跟踪抓取、TPD跟踪设为0
	 * @param [in] para[4] 是否配视觉  0 不配  1 配
	 * @param [in] para[5] 速度比  针对传送带跟踪抓取选项（1-100）  其他选项默认为1 
	 * @return 错误码
	 */
    errno_t ConveyorSetParam(float param[5]);

传动带抓取点补偿
+++++++++++++++++++++++++++++++++++++++++++++
.. versionchanged:: C++SDK-v2.1.2.0

.. code-block:: c++
    :linenos:

	/**
	 * @brief 传动带抓取点补偿
	 * @param [in] cmp 补偿位置 double[3]{x, y, z}
	 * @return 错误码
	 */
    errno_t ConveyorCatchPointComp(double cmp[3]);

直线运动
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 直线运动
     * @param [in] status 状态，1-启动，0-停止 
     * @return 错误码
     */
    errno_t TrackMoveL(char name[32], int tool, int wobj, float vel, float acc, float ovl, float blendR, uint8_t flag, uint8_t type);

获取SSH公钥
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取SSH公钥
     * @param [out] keygen 公钥
     * @return 错误码
     */
    errno_t GetSSHKeygen(char keygen[1024]);

下发SCP指令
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 下发SCP指令
     * @param [in] mode 0-上传（上位机->控制器），1-下载（控制器->上位机）
     * @param [in] sshname 上位机用户名
     * @param [in] sship 上位机ip地址
     * @param [in] usr_file_url 上位机文件路径
     * @param [in] robot_file_url 机器人控制器文件路径
     * @return 错误码
     */
    errno_t SetSSHScpCmd(int mode, char sshname[32], char sship[32], char usr_file_url[128], char robot_file_url[128]);

计算指定路径下文件的MD5值
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 计算指定路径下文件的MD5值
     * @param [in] file_path 文件路径包含文件名，默认Traj文件夹路径为:"/fruser/traj/",如"/fruser/traj/trajHelix_aima_1.txt"
     * @param [out] md5 文件MD5值
     * @return 错误码
     */
    errno_t ComputeFileMD5(char file_path[256], char md5[256]);

获取机器人急停状态
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取机器人急停状态
     * @param [out] state 急停状态，0-非急停，1-急停
     * @return 错误码  
     */
    errno_t GetRobotEmergencyStopState(uint8_t *state);

获取SDK与机器人的通讯状态
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取SDK与机器人的通讯状态
     * @param [out]  state 通讯状态，0-通讯正常，1-通讯异常
     */
    errno_t GetSDKComState(int *state);

获取安全停止信号
+++++++++++++++++++++++++++++++++++++++++++++
.. code-block:: c++
    :linenos:

    /**
     * @brief 获取安全停止信号
     * @param [out]  si0_state 安全停止信号SI0，0-无效，1-有效
     * @param [out]  si1_state 安全停止信号SI1，0-无效，1-有效
     */
    errno_t GetSafetyStopState(uint8_t *si0_state, uint8_t *si1_state);

代码示例
+++++++++++++++
.. code-block:: c++
    :linenos:

    #include <cstdlib>
    #include <iostream>
    #include <stdio.h>
    #include <cstring>
    #include <unistd.h>
    #include "FRRobot.h"
    #include "RobotTypes.h"

    using namespace std;

    int main(void)
    {
        FRRobot robot;                 //实例化机器人对象
        robot.RPC("192.168.58.2");     //与机器人控制器建立通信连接

        uint8_t flag = 1;
        int sensor_id = 1;
        uint8_t select[6] = {1,1,1,0,0,0};
        float ft_pid[6] = {0.0005,0.0,0.0,0.0,0.0,0.0};
        uint8_t adj_sign = 0;
        uint8_t ILC_sign = 0;
        float max_dis = 100.0;
        float max_ang = 0.0;

        ForceTorque ft;
        DescPose desc_p1, desc_p2, offset_pos;
        ExaxisPos epos;
        JointPos j1, j2;
        memset(&ft, 0, sizeof(ForceTorque));
        memset(&desc_p1, 0, sizeof(DescPose));
        memset(&desc_p2, 0, sizeof(DescPose));
        memset(&offset_pos, 0, sizeof(DescPose));
        memset(&j1, 0, sizeof(JointPos));
        memset(&j2, 0, sizeof(JointPos));
        memset(&epos, 0, sizeof(ExaxisPos));

        j1 = {-105.3,-68.0,-127.9,-75.5,90.8,77.8};
        j2 = {-105.3,-97.9,-101.5,-70.3,90.8,77.8};

        desc_p1.tran.x = -208.9;
        desc_p1.tran.y = -274.5;
        desc_p1.tran.z = 334.6;
        desc_p1.rpy.rx = 178.8;
        desc_p1.rpy.ry = -1.3;
        desc_p1.rpy.rz = 86.7;

        desc_p2.tran.x = -264.8;
        desc_p2.tran.y = -480.5;
        desc_p2.tran.z = 341.8;
        desc_p2.rpy.rx = 179.2;
        desc_p2.rpy.ry = 0.3;
        desc_p2.rpy.rz = 86.7;

        ft.fx = -10.0;
        ft.fy = -10.0;
        ft.fz = -10.0;
        robot.FT_Control(flag, sensor_id, select, &ft, ft_pid, adj_sign, ILC_sign, max_dis, max_ang);  
        float p = 0.00005;
        float force = 30.0; 
        robot.FT_ComplianceStart(p, force); 
        int count = 15;
        while (count)
        {
            robot.MoveL(&j1,&desc_p1,9,0,100.0,180.0,100.0,-1.0,&epos,0,1,&offset_pos);
            robot.MoveL(&j2,&desc_p2,9,0,100.0,180.0,100.0,-1.0,&epos,0,0,&offset_pos);
            count -= 1;
        }
        robot.FT_ComplianceStop();
        flag = 0;
        robot.FT_Control(flag, sensor_id, select, &ft, ft_pid, adj_sign, ILC_sign, max_dis, max_ang);

        return 0;
    }

代码示例
+++++++++++++++
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
    #include <string>

    using namespace std;

    int main(void)
    {
        FRRobot robot;         
        robot.RPC("192.168.58.2"); 

        int retval = 0;

        retval = robot.LoadIdentifyDynFilterInit();
        printf("LoadIdentifyDynFilterInit retval is: %d \n", retval);

        retval = robot.LoadIdentifyDynVarInit();
        printf("LoadIdentifyDynVarInit retval is: %d \n", retval);

        double joint_toq[6] = {0};
        double joint_pos[6] = {0};
        retval = robot.LoadIdentifyMain(joint_toq, joint_pos,1);
        printf("LoadIdentifyMain retval is: %d \n", retval);

        double gain[12] = {0};
        double weight = 0;
        DescTran load_pos;
        memset(&load_pos, 0, sizeof(DescTran));
        retval = robot.LoadIdentifyGetResult(gain, &weight, &load_pos);
        printf("LoadIdentifyGetResult retval is: %d \n", retval);
        printf("weight is: %f, load pose is: %f, %f, %f\n", weight, load_pos.x, load_pos.y, load_pos.z);

        retval = robot.WaitMs(10);
        printf("WaitMs retval is: %d \n", retval);
    }

代码示例
+++++++++++++++
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
    #include <string>

    using namespace std;

    int main(void)
    {
        FRRobot robot; 
        robot.RPC("192.168.58.2"); 

        int retval = 0;

        retval = robot.ConveyorStartEnd(1);
        printf("ConveyorStartEnd retval is: %d\n", retval);

        retval = robot.ConveyorPointIORecord();
        printf("ConveyorPointIORecord retval is: %d\n", retval);

        retval = robot.ConveyorPointARecord();
        printf("ConveyorPointARecord retval is: %d\n", retval);

        retval = robot.ConveyorRefPointRecord();
        printf("ConveyorRefPointRecord retval is: %d\n", retval);

        retval = robot.ConveyorPointBRecord();
        printf("ConveyorPointBRecord retval is: %d\n", retval);

        retval = robot.ConveyorStartEnd(0);
        printf("ConveyorStartEnd retval is: %d\n", retval);
        
        retval = 0;
        float param[6] ={1,10000,200,0,0,20};
        retval = robot.ConveyorSetParam(param);
        printf("ConveyorSetParam retval is: %d\n", retval);
        
        double cmp[3] = {0.0, 0.0, 0.0};
        retval = robot.ConveyorCatchPointComp(cmp);
        printf("ConveyorCatchPointComp retval is: %d\n", retval);

        int index = 1;
        int max_time = 30000;
        uint8_t block = 0;
        retval = 0;
        
        /* 下面是一个传送带抓取流程 */
        DescPose desc_p1;
        desc_p1.tran.x = -351.553;
        desc_p1.tran.y = 87.913;
        desc_p1.tran.z = 354.175;
        desc_p1.rpy.rx = -179.680;
        desc_p1.rpy.ry =  -0.133;
        desc_p1.rpy.rz = 2.472;

        DescPose desc_p2;
        desc_p2.tran.x = -351.535;
        desc_p2.tran.y = -247.222;
        desc_p2.tran.z = 354.173;
        desc_p2.rpy.rx = -179.680;
        desc_p2.rpy.ry =  -0.137;
        desc_p2.rpy.rz = 2.473;


        retval = robot.MoveCart(&desc_p1, 1, 0, 100.0, 100.0, 100.0, -1.0, -1);
        printf("MoveCart retval is: %d\n", retval);

        retval = robot.WaitMs(1);
        printf("WaitMs retval is: %d\n", retval);

        retval = robot.ConveyorIODetect(10000);
        printf("ConveyorIODetect retval is: %d\n", retval);

        retval = robot.ConveyorGetTrackData(1);
        printf("ConveyorGetTrackData retval is: %d\n", retval);

        retval = robot.ConveyorTrackStart(1);
        printf("ConveyorTrackStart retval is: %d\n", retval);

        retval = robot.TrackMoveL("cvrCatchPoint",  1, 0, 100, 100, 100, -1.0, 0, 0);
        printf("TrackMoveL retval is: %d\n", retval);

        retval = robot.MoveGripper(index, 51, 40, 30, max_time, block);
        printf("MoveGripper retval is: %d\n", retval);

        retval = robot.TrackMoveL("cvrRaisePoint", 1, 0, 100, 100, 100, -1.0, 0, 0);
        printf("TrackMoveL retval is: %d\n", retval);

        retval = robot.ConveyorTrackEnd();
        printf("ConveyorTrackEnd retval is: %d\n", retval);

        robot.MoveCart(&desc_p2, 1, 0, 100.0, 100.0, 100.0, -1.0, -1);

        retval = robot.MoveGripper(index, 100, 40, 10, max_time, block);
        printf("MoveGripper retval is: %d\n", retval);

        return 0;
    }

代码示例
+++++++++++++++
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
    #include <string>

    using namespace std;

    int main(void)
    {
        FRRobot robot;           
        robot.RPC("192.168.58.2");

        char file_path[256] = "/fruser/traj/test_computermd5.txt.txt";
        char md5[256] = {0};
        uint8_t emerg_state = 0;
        uint8_t si0_state = 0;
        uint8_t si1_state = 0;
        int sdk_com_state = 0;

        char ssh_keygen[1024] = {0};
        int retval = robot.GetSSHKeygen(ssh_keygen);
        printf("GetSSHKeygen retval is: %d\n", retval);
        printf("ssh key is: %s \n", ssh_keygen);

        char ssh_name[32] = "fr";
        char ssh_ip[32] = "192.168.58.44";
        char ssh_route[128] = "/home/fr";
        char ssh_robot_url[128] = "/root/robot/dhpara.config";
        retval = robot.SetSSHScpCmd(1, ssh_name, ssh_ip, ssh_route, ssh_robot_url);
        printf("SetSSHScpCmd retval is: %d\n", retval);
        printf("robot url is: %s\n", ssh_robot_url);

        robot.ComputeFileMD5(file_path, md5);
        printf("md5 is: %s \n", md5);

        robot.GetRobotEmergencyStopState(&emerg_state);
        printf("emergency state is: %u \n", emerg_state);

        robot.GetSafetyStopState(&si0_state, &si1_state);
        printf("safety stop state is: %u, %u \n", si0_state, si1_state);

        robot.GetSDKComState(&sdk_com_state);
        printf("sdk com state is: %d", sdk_com_state);
        return 0;
    }