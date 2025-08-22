
```dataviewjs
// 配置路径
const 比赛路径 = "3-文档/2-比赛";
const 题解路径 = "2-题解";
const 笔记路径 = "3-文档/1-笔记";

// 分类状态 - 设置"最近刷题"为默认
let 当前分类方式 = "最近刷题"; // "比赛", "知识点", "最近刷题"
let 隐藏已完成比赛 = false; // 是否隐藏补题数量为0的比赛

// 获取所有题解文件
const 所有题解 = dv.pages(`"${题解路径}"`);

// 创建控制按钮容器
const 控制容器 = document.createElement("div");
控制容器.style.textAlign = "center";
控制容器.style.marginBottom = "15px";
控制容器.style.padding = "10px";
控制容器.style.backgroundColor = "var(--background-secondary)";
控制容器.style.borderRadius = "8px";

// 创建分类选择按钮
const 分类按钮组 = ["最近刷题", "比赛", "知识点"];
const 按钮样式 = {
  border: "none",
  margin: "0 5px",
  padding: "8px 15px",
  fontSize: "14px",
  borderRadius: "4px",
  cursor: "pointer",
  transition: "all 0.2s ease"
};

// 初始化按钮主题
function 初始化按钮主题(button, active) {
  if (active) {
    button.style.color = "var(--text-on-accent)";
    button.style.backgroundColor = "var(--interactive-accent)";
  } else {
    button.style.color = "var(--text-normal)";
    button.style.backgroundColor = "var(--background-primary)";
  }
}

// 创建分类按钮
分类按钮组.forEach(分类名 => {
  const 按钮 = document.createElement("button");
  Object.assign(按钮.style, 按钮样式);
  按钮.textContent = `${分类名}`;
  
  按钮.addEventListener("click", () => {
    当前分类方式 = 分类名;
    更新显示();
    // 更新所有按钮状态
    控制容器.querySelectorAll("button").forEach(btn => {
      if (btn.textContent === 分类名) {
        初始化按钮主题(btn, true);
      } else {
        初始化按钮主题(btn, false);
      }
    });
  });
  
  控制容器.appendChild(按钮);
});

// 添加到页面
dv.container.appendChild(控制容器);

// 创建比赛过滤按钮容器（初始隐藏）
const 比赛过滤容器 = document.createElement("div");
比赛过滤容器.style.textAlign = "center";
比赛过滤容器.style.marginBottom = "15px";
比赛过滤容器.style.padding = "8px";
比赛过滤容器.style.backgroundColor = "var(--background-modifier-form-field)";
比赛过滤容器.style.borderRadius = "6px";
比赛过滤容器.style.display = "none"; // 初始隐藏

// 创建比赛过滤按钮
const 过滤按钮 = document.createElement("button");
Object.assign(过滤按钮.style, 按钮样式);
过滤按钮.textContent = "隐藏已完成比赛";

过滤按钮.addEventListener("click", () => {
  隐藏已完成比赛 = !隐藏已完成比赛;
  过滤按钮.textContent = 隐藏已完成比赛 ? "显示已完成比赛" : "隐藏已完成比赛";
  初始化按钮主题(过滤按钮, 隐藏已完成比赛);
  更新显示();
});

比赛过滤容器.appendChild(过滤按钮);
dv.container.appendChild(比赛过滤容器);

// 统计比赛文档中未完成任务的函数
function 统计未完成任务(比赛文档路径) {
  try {
    const 比赛文档 = dv.page(比赛文档路径);
    if (!比赛文档 || !比赛文档.file.tasks) {
      return 0;
    }
    
    // 统计未完成的任务（completed 为 false 的任务）
    const 未完成任务 = 比赛文档.file.tasks.filter(task => !task.completed);
    return 未完成任务.length;
  } catch (error) {
    return 0;
  }
}

// 数据处理函数
function 获取比赛数据() {
  const 比赛映射 = {};
  const 题解比赛映射 = new Map();
  const 比赛文档映射 = new Map(); // 存储比赛名称到文档路径的映射

  for (let 题解 of 所有题解) {
    const 反向链接 = 题解.file.inlinks;
    let 所属比赛 = null;
    let 比赛文档路径 = null;
    
    for (let 链接 of 反向链接) {
      const 链接文件 = dv.page(链接.path);
      if (链接文件?.file?.folder?.includes(比赛路径)) {
        所属比赛 = 链接文件.file.name.replace(".md", "");
        比赛文档路径 = 链接.path;
        break;
      }
    }
    
    if (所属比赛) {
      题解比赛映射.set(题解.file.path, 所属比赛);
      比赛文档映射.set(所属比赛, 比赛文档路径);
      if (!比赛映射[所属比赛]) 比赛映射[所属比赛] = [];
      比赛映射[所属比赛].push(题解);
    }
  }

  let 比赛数据 = Object.entries(比赛映射)
    .map(([比赛, 题解列表]) => {
      const 比赛文档路径 = 比赛文档映射.get(比赛);
      const 比赛文档 = dv.page(比赛文档路径);
      const 未完成任务数 = 统计未完成任务(比赛文档路径);
      
      return {
        名称: 比赛,
        数量: 题解列表.length,
        题解: 题解列表,
        链接: `[[${比赛路径}/${比赛}|${比赛}]]`,
        未完成任务: 未完成任务数,
        创建时间: 比赛文档?.file?.ctime || new Date(0)
      };
    });

  // 根据是否隐藏已完成比赛进行过滤
  if (隐藏已完成比赛) {
    比赛数据 = 比赛数据.filter(项 => 项.未完成任务 > 0);
  }

  // 统一按时间排序，越新的放在前面
  比赛数据 = 比赛数据.sort((a, b) => b.创建时间 - a.创建时间);

  return { 分类数据: 比赛数据, 题解比赛映射 };
}

function 获取知识点数据() {
  const 标签映射 = {};

  for (let 题解 of 所有题解) {
    const 标签列表 = 题解.file.tags || [];
    const 有效标签 = 标签列表.filter(标签 => 标签.replace("#", "") !== "题解");
    
    for (let 标签 of 有效标签) {
      const 纯净标签 = 标签.replace("#", "");
      if (!标签映射[纯净标签]) 标签映射[纯净标签] = [];
      标签映射[纯净标签].push(题解);
    }
  }

  const 标签数据 = Object.entries(标签映射)
    .map(([标签, 题解列表]) => ({
      名称: 标签,
      数量: 题解列表.length,
      题解: 题解列表,
      链接: `[[${笔记路径}/${标签}|#${标签}]]`
    }))
    .sort((a, b) => b.数量 - a.数量);

  return { 分类数据: 标签数据 };
}

// 更新显示函数
function 更新显示() {
  // 显示或隐藏比赛过滤按钮
  if (当前分类方式 === "比赛") {
    比赛过滤容器.style.display = "block";
    // 更新过滤按钮状态
    初始化按钮主题(过滤按钮, 隐藏已完成比赛);
  } else {
    比赛过滤容器.style.display = "none";
  }

  // 清空内容区域（保留控制按钮和过滤按钮）
  const 当前内容 = dv.container.children;
  for (let i = 当前内容.length - 1; i >= 2; i--) {
    当前内容[i].remove();
  }

  if (当前分类方式 === "最近刷题") {
    // 显示最近刷题
    const 最近容器 = document.createElement("div");
    最近容器.innerHTML = `<h3>⏱️ 最近刷题</h3>`;
    dv.container.appendChild(最近容器);

    const 最近数据 = 所有题解
      .sort(p => p.file.ctime, 'desc')
      .slice(0,100)
      .map(p => {
        const 知识点 = (p.file.tags || []).filter(t => t !== "#题解").map(t => t.replace("#", "")).join(", ");
        
        return [
          p.file.link,
          知识点,
          p.file.ctime.toRelative()
        ];
      });

    dv.table(
      ["题目", "知识点", "创建时间"],
      最近数据
    );
  } else {
    // 获取数据
    let 数据结果;
    let 标题图标;
    let 表头;

    if (当前分类方式 === "比赛") {
      数据结果 = 获取比赛数据();
      标题图标 = "🏆";
      表头 = ["比赛名称", "题解数量", "计划补题"];
    } else if (当前分类方式 === "知识点") {
      数据结果 = 获取知识点数据();
      标题图标 = "🏷️";
      表头 = ["知识点标签", "题解数量"];
    }

    // 显示分类表格
    const 分类容器 = document.createElement("div");
    分类容器.innerHTML = `<h3>${标题图标} ${当前分类方式}分类</h3>`;
    dv.container.appendChild(分类容器);

    let 表格数据;
    if (当前分类方式 === "比赛") {
      // 比赛分类包含计划补题列
      表格数据 = 数据结果.分类数据.map(项 => [
        项.链接, 
        项.数量, 
        项.未完成任务 > 0 ? 项.未完成任务.toString() : ""
      ]);
    } else {
      // 知识点分类只有名称和数量
      表格数据 = 数据结果.分类数据.map(项 => [项.链接, 项.数量]);
    }
    
    dv.table(表头, 表格数据);
  }
}

// 初始化显示 - 默认选中"最近刷题"（第一个按钮）
初始化按钮主题(控制容器.children[0], true); 
更新显示();
```

