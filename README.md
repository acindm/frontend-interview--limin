# frontend-interview_limin
# 1. 用HTML+CSS实现一个可拖拽的积木块（尺寸100x100px，背景色为蓝色），拖拽时积木块透明度变为0.5，并限制拖拽范围在父容器（500x500px）内。
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>可拖拽积木块</title>
    <style>
        /* 父容器 */
        .container {
            position: relative;
            width: 500px;
            height: 500px;
            border: 2px solid #333;
            margin: 20px auto;
            overflow: hidden;
        }
        
        /* 积木块 */
        .block {
            position: absolute;
            width: 100px;
            height: 100px;
            background-color: blue;
            cursor: grab;
            user-select: none; /* 防止拖动时选中文本 */
        }
        
        .dragging {
            opacity: 0.5;
            cursor: grabbing;
        }
    </style>
</head>
<body>
    <div class="container" id="container">
        <div class="block" id="block"></div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const block = document.getElementById('block');
            const container = document.getElementById('container');
            
            let isDragging = false;
            let offsetX, offsetY;
            
            // 拖拽时半透明
            block.addEventListener('mousedown', function(e) {
                isDragging = true;
                block.classList.add('dragging');
                
                const rect = block.getBoundingClientRect();
                offsetX = e.clientX - rect.left;
                offsetY = e.clientY - rect.top;
                
                e.preventDefault();
            });
            
            // 拖拽过程
            document.addEventListener('mousemove', function(e) {
                if (!isDragging) return;
                
                let newX = e.clientX - offsetX - container.getBoundingClientRect().left;
                let newY = e.clientY - offsetY - container.getBoundingClientRect().top;
                
                newX = Math.max(0, Math.min(newX, container.clientWidth - block.clientWidth));
                newY = Math.max(0, Math.min(newY, container.clientHeight - block.clientHeight));
                
                // 更新位置
                block.style.left = newX + 'px';
                block.style.top = newY + 'px';
            });
            
            // 结束拖拽
            document.addEventListener('mouseup', function() {
                if (isDragging) {
                    isDragging = false;
                    block.classList.remove('dragging'); // 恢复透明度
                }
            });
            
            document.addEventListener('mouseleave', function() {
                if (isDragging) {
                    isDragging = false;
                    block.classList.remove('dragging');
                }
            });
        });
    </script>
</body>
</html>


# 2. 编写run函数，使该代码能正常运行，不报错
function run() {
  Object.prototype[Symbol.iterator] = function() {
    return Object.values(this)[Symbol.iterator]();
  };
}
run();
const [a, b] = {a: 1, b: 2} ;
console.log(a, b); // 输出 1 2

# 3. 给定一组图形化代码块的json数据，编写函数将其转换为JavaScript代码字符串。
function convertBlocksToJS(blocksData) {
  function processBlock(block) {
    if (!block) return "";
    
    switch (block.type) {
      case "当开始运行":
        return `当开始运行(() => {\n  ${processBlock(block.next)}\n});`;
        
      case "永远循环":
        return `永远循环(() => {\n    ${processBlock(block.statements.DO)}\n  });`;
        
      case "如果":
        const condition = processInput(block.inputs.IF0);
        const thenBlock = block.statements.DO0 ? processBlock(block.statements.DO0) : "";
        const elseBlock = block.statements.ELSE ? processBlock(block.statements.ELSE) : "";
        return `if (${condition}) {\n      ${thenBlock}\n    } else {\n      ${elseBlock}\n    }`;
        
      case "移动步数":
        const steps = processInput(block.inputs.steps);
        return `移动步数(${steps});`;
        
      case "移到位置":
        const x = processInput(block.inputs.x);
        const y = processInput(block.inputs.y);
        return `移到位置(${x}, ${y});`;
        
      case "判断角色碰撞":
        return `判断角色碰撞("${block.fields.sprite}", "${block.fields.sprite1}")`;
        
      default:
        return `/* 未知块类型: ${block.type} */`;
    }
  }
  
  // 处理输入值
  function processInput(input) {
    if (!input) return "";
    
    if (input.type === "math_number") {
      return input.fields.NUM;
    } else if (input.type === "判断角色碰撞") {
      return `判断角色碰撞("${input.fields.sprite}", "${input.fields.sprite1}")`;
    }
    
    return `/* 未知输入类型 */`;
  }
  
  return processBlock(blocksData);
}
