# BeanUtils

<font color="yellow">BeanUtils.copyProperties(sysUser, this); 仅对相同的字段进行自动赋值</font>

[参考资料]: https://blog.csdn.net/dongyuxu342719/article/details/90242904

```java
//包的来源
org.springframework.beans.BeanUtils;


    public UserVo createVo(SysUser sysUser, SysUserLog sysUserLog, SysUserRole sysUserRole, SysAdminarea province, SysAdminarea city, SysAdminarea county){
        //字段类型和大小写不对应都无法赋值
        BeanUtils.copyProperties(sysUser, this);
        if(sysUser.getPastTime() != null){
            long nowTime = DateUtil.getCurrentTime();
            long pastTime = DateUtil.getTime(DateUtil.formatDate(sysUser.getPastTime(), "yyyy-MM-dd"), "yyyy-MM-dd");
            if(nowTime > pastTime){
                this.past = UserPastEnum.PAST_NO.getCode();
            }else{
                this.past = UserPastEnum.PAST_YES.getCode();
            }
        }else{
            this.past = UserPastEnum.PAST_NO.getCode();
        }
        if(sysUserLog != null){
            this.checkTime = sysUserLog.getCreateTime();
        }

        if(province != null){
            provinceName = province.getQhmc();
        }
        if(city != null){
            this.cityName = city.getQhmc();
        }
        if(county != null){
            this.countyName = county.getQhmc();
        }

        if(sysUserRole != null){
            this.roleId = sysUserRole.getRoleId();
        }
        return this;
    }
```

