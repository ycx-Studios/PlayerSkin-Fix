# PlayerSkin (支持权限 Flag 适配与数据库) | [English](README.md)

## 写在前面
首先感谢你使用我的插件! 非常感谢原作者: [safra36](https://github.com/safra36/PlayerSkin)  
原作者已经有数年不更新此插件了, 由于插件平台的更新, 原插件有许多问题, 并且现在已经几乎无法使用.  
我修复了那些目前存在的问题, 并打算在此发布编译完成的插件, 目前暂时不打算开源.  
我打算对插件进行重写以及对功能上的更新, 待到那时也许会公开发布新版本.  
欢迎提出建议, 以及我可以提供插件的有限支持, 很高兴能帮到你.  

## 描述
这是一个用于管理玩家模型与手臂模型的简单插件, 大部分功能想法基于玩家与 AlliedModders 论坛用户的建议.
使用这个插件可以实现以下功能:
- 为不同地图配置可以使用的皮肤;
- 不同用户组可以使用的皮肤;
- 在选择菜单中为皮肤分组;
- 不同玩家可以使用的专属皮肤;
- SQLite + MySQL 数据库支持;
- 支持由本插件直接添加文件下载功能;
- 仍有更多功能待你发现...

## 需求
- 最新的 Metamod 开发构建
- 最新的 Sourcemod (目前版本为 1.10)
- 需要手臂修复插件, 默认包含在插件中. 获取关于此功能的更多信息, 请查看 [增加手臂功能](https://github.com/safra36/PlayerSkin/wiki/Adding-Arms-Capability)

## 最新版本的重要更新
- 文件路径与名称已经更改, 别忘了修改它们 (移动到了一个单独的文件夹, 不再使用单个配置文件)
***
- 修复了数据库查询错误的问题 [#19](https://github.com/safra36/PlayerSkin/issues/19)
- 修复了 `sm_round_timeout` cvar 不生效的问题 [#16](https://github.com/safra36/PlayerSkin/issues/16)
- 添加了文件下载功能, 不需要额外插件下载模型文件, 具体请查看下方 `download.ini` 部分
- 修复了复活后更换的皮肤失效问题 [#25](https://github.com/safra36/PlayerSkin/issues/25)
- 更多插件优化

**位于 AlliedModders 上的链接:** https://forums.alliedmods.net/showthread.php?t=293846

## ConVars
- `sm_pskin_enable 1` - 启用/禁用在聊天框中输入的 `!pskin` 命令. ***(如果你只想为管理员启用自动分配皮肤, 那么这个参数很有用)***
- `sm_cat_enable 0` - 启用/禁用 categories.ini 文件的皮肤分组支持. ***(若要使用此功能, 请查看下方的配置章节)***
- `sm_start_menu 0` - 启用/禁用回合开始时为玩家显示菜单.
- `sm_hide_options 0` - 为用户隐藏无权使用的皮肤选项.
- `sm_hide_teams 0` - 为用户隐藏无法使用的其他阵营皮肤选项.
- `sm_mapskins_enable 1` - 是否允许用户选择应用地图皮肤.
- `sm_round_timeout 20.0` - 限制回合开始后可以使用 `!pskin` 命令的时间. ***(设置为 0.0 则禁用这项设置)***
- `sm_ct_skin ""` - CT 阵营的默认皮肤.
- `sm_t_skin ""` -  T 阵营的默认皮肤.
- `sm_ct_arm ""` - CT 阵营的默认手臂皮肤.
- `sm_t_arm ""` - T 阵营的默认手臂皮肤.

## 配置

### 配置数据库
- 为了让插件能正常与数据库连接, 你需要在 `databases.cfg` 文件中为插件添加一条语句, 语句如下:
```
"Databases"
{
    "PlayerSkins"
	{
		"driver"		"sqlite"
		"database"		"PlayerSkins"
	}
}
```

### 配置 `skins.ini`
- 此文件由菜单读取, 当玩家在聊天中触发 `"!skins"` 时, 插件将会向用户显示该菜单, 此文件也被分组文件使用:
```
"Skins" {
    "santagirl" // 选项的名称, 不太重要, 填什么都可以, 也可以重复
    {
        "Name"        	"Santa Girl [T]" 																	// 将作为皮肤选择菜单中皮肤的显示名称
        "Skin"       	"models/player/custom_player/kuristaja/cso2/natalie_santagirl/natalie.mdl" 			// 皮肤模型的路径
        "Arms"       	"models/player/custom_player/kuristaja/cso2/natalie_santagirl/natalie_arms.mdl" 	// 皮肤手臂模型的路径
        "Team"        	"2" 																				// 可以使用皮肤的队伍编号 (2 = 恐怖分子, 3 = 反恐精英, 留空为两队均可使用)
        "Flag"			"z" 																				// 允许使用此皮肤的权限 Flag (留空为公用皮肤)
        "u_id"       	"skin_santagirl" 																	// 皮肤的唯一 ID, 不可重复 (用于检测用户使用的皮肤)
        "catgroup"    	"cat_female" 																		// 分组 ID, 用于菜单中的分类
    }
 } 
```

### 配置 `admin_skins.ini`
- 此文件用于设置系统对用户自动更改的皮肤.
```
"Admin_Skins" {
    "USER_FLAGS_CHAR" // 权限 Flag 字符串
    {
        //如果不想给这个 Flag 设置队伍默认皮肤, 那可以将他们留空.
        "SkinT" 	"此 Flag 使用的默认 T 皮肤路径"
        "ArmsT" 	"此 Flag 使用的默认 T 手臂皮肤路径"
        "SkinCT"    "此 Flag 使用的默认 CT 皮肤路径"
        "ArmsCT"    "此 Flag 使用的默认 CT 手臂皮肤路径"
    }
    // 这是一个预置的配置, 用于非管理员玩家
    "def"
    {
        "SkinT" 	"非管理员使用的默认 T 皮肤路径"
        "ArmsT" 	"非管理员使用的默认 T 手臂皮肤路径"
        "SkinCT"    "非管理员使用的默认 CT 皮肤路径"
        "ArmsCT"    "非管理员使用的默认 CT 手臂皮肤路径"
    }
    "z"
    {
        "SkinT" 	"models/player/custom_player/kuristaja/deadpool/deadpool.mdl"
        "ArmsT"  	"models/player/custom_player/kuristaja/deadpool/deadpool_arms.mdl"
        "SkinCT"    "models/player/custom_player/kuristaja/ak/batman/batmanv2.mdl"
        "ArmsCT"    "models/player/custom_player/kuristaja/ak/batman/batman_arms.mdl"
    }
 } 
```

### 配置 `categories.ini`
- 在打开相关 cvar 后, 可以使用此文件设置分组功能
```
"Categories" {
    "EXAMPLE_CATEGORY" // 示例分组
    {
        "Name"        "在菜单中显示的名称"
        "Flag"        "可以在菜单中显示并使用的 Flag 权限"
        "u_id"        "菜单中的唯一 ID"
		"catgroup"    "cat_ID"
    }
    "Admins"
    {
        "Name"        "管理员"
        "Flag"        "z"
        "u_id"        "menu_admin"
        "catgroup"    "cat_admin"
    }
    "Vips"
    {
        "Name"        "会员"
        "Flag"        "r"
        "u_id"        "menu_vip"
        "catgroup"    "cat_vip"
    }
    "Users"
    {
        "Name"        "普通用户"
        "Flag"        ""
        "u_id"        "menu_users"
        "catgroup"    "cat_normal"
    }
 }  
```

### 为特定的 SteamID 用户配置皮肤
- 使用 Steam 64 位 ID 为特定的用户配置专属皮肤
```
"userids" {
    "SteamId_64"
    {
        "CT" // 队伍编号 = 3 (除了CSGO, 其他游戏需要使用队伍编号)
        {
            "Skin"    "模型路径"
            "Arms"    "手臂模型路径"
        }
        "T" // 队伍编号 = 2 (除了CSGO, 其他游戏需要使用队伍编号)
        {
            "Skin"    "模型路径"
            "Arms"    "手臂模型路径"
        }
    }
    "76561198123013657"
    {
        "CT"
        {
            "Skin"    "models/player/custom_player/caleon1/harleyquinn/harleyquinn.mdl"
            "Arms"    "models/player/custom_player/caleon1/harleyquinn/harleyquinn_arms.mdl"
        }
        "T"
        {
            "Skin"    "models/player/custom_player/kuristaja/cso2/natalie_santagirl/natalie.mdl"
            "Arms"    "models/player/custom_player/kuristaja/cso2/natalie_santagirl/natalie_arms.mdl"
        }
    }
 }  
```

### 为地图配置皮肤
- 允许你为特定的地图配置专属皮肤 ***(需要指定完整地图名称, 不可使用前缀通配)***
```
"mapskins" {
    "MAP_NAME" // 地图名称
    {
        "CT"
        {
            "Skin"        ""
            "Arms"        ""
        }
        "T"
        {
            "Skin"        ""
            "Arms"        ""
        }
    }
	"de_dust2"
    {
        "CT"
        {
            "Skin"        "models/player/custom_player/kuristaja/cso2/gsg9/gsg9.mdl"
            "Arms"        "models/player/custom_player/kuristaja/cso2/gsg9/gsg9_arms.mdl"
        }
        "T"
        {
            "Skin"        "models/player/custom_player/kuristaja/cso2/gsg9/gsg9.mdl"
            "Arms"        "models/player/custom_player/kuristaja/cso2/gsg9/gsg9_arms.mdl"
        }
    }
 } 
```

### 配置文件下载清单
- 此文件用于玩家进入服务器时需要下载的文件路径, 每行一个文件
```
models/player/custom_player/kuristaja/cso2/gsg9/gsg9.mdl
models/player/custom_player/kuristaja/cso2/gsg9/gsg9.dx90.vtx
models/player/custom_player/kuristaja/cso2/gsg9/gsg9.vvd
models/player/custom_player/kuristaja/cso2/gsg9/gsg9_arms.mdl
models/player/custom_player/kuristaja/cso2/gsg9/gsg9_arms.dx90.vtx
models/player/custom_player/kuristaja/cso2/gsg9/gsg9v.vvd
materials/models/player/kuristaja/cso2/gsg9/ct_gsg.vmt
materials/models/player/kuristaja/cso2/gsg9/ct_gsg.vtf
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_glass.vmt
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_glass.vtf
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_glass_normal.vtf
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_hand.vmt
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_hand.vtf
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_hand_normal.vtf
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_multi.vtf
materials/models/player/kuristaja/cso2/gsg9/ct_gsg_normal.vtf
```


## 为手套插件添加支持
在 `plugins/disabled` 中有一个叫做 `PlayerSkin_Gloves.smx` 的文件, 将它替换掉原来 `plugins` 目录中的 `PlayerSkin.smx` 即可.

***

我不希望插件有额外的版本, 所以在此我添加了这段代码以便于你修改它与之兼容. 为了启用这段代码, 请自行将 `202 到 217 行` 的代码取消注释.
别忘了也要取消文件最上方的 `#include` 注释

```
stock bool SetModels(int client, char[] model, char[] arms)
{
	if(!IsModelPrecached(model))
	{
		PrecacheModel(model)
	}
	
	if(!IsModelPrecached(arms))
	{
		PrecacheModel(arms)
	}
	
	if(!StrEqual(model, "", false))
	{
		SetEntityModel(client, model);

		if(!IsClientWithArms(client))
		{
			if(!StrEqual(arms, "", false))
			{
				SetEntPropString(client, Prop_Send, "m_szArmsModel", arms);
				// Gloves_SetArmsModel(client, arms);
			}
			else
			{
				int g_iTeam = GetClientTeam(client);
				if(g_iTeam == 2)
				{
					SetEntPropString(client, Prop_Send, "m_szArmsModel", defArms[1]);
					// Gloves_SetArmsModel(client, defArms[1]);
				}
				else if(g_iTeam == 3)
				{
					SetEntPropString(client, Prop_Send, "m_szArmsModel", defArms[0]);
					// Gloves_SetArmsModel(client, defArms[0]);
				}
			}
		}
		else
		{
			PrintToServer("[PlayerSkin] Gloves detected, skipping setting arms ...");
		}
		
		return true;
	}
	else
	{
		return false;
	}
}
```