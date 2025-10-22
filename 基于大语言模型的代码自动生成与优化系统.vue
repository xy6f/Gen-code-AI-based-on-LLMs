<template>
  <div class="code-generator-app min-h-screen bg-blue-50 text-gray-800 flex flex-col">
    <!-- 顶部导航栏 -->
    <header class="bg-white border-b border-blue-100 py-3 px-4 sm:px-6 shadow-sm">
      <div class="container mx-auto flex justify-between items-center">
        <div class="flex items-center space-x-2">
          <div class="w-8 h-8 rounded-md bg-gradient-to-br from-blue-400 to-cyan-500 flex items-center justify-center">
            <code-icon class="w-5 h-5 text-white" />
          </div>
          <h1 class="text-xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-blue-600 to-cyan-600">
            CodeGenius
          </h1>
          <span class="text-xs bg-blue-100 text-blue-700 px-2 py-0.5 rounded-full">Beta</span>
        </div>
        
        <div class="flex items-center space-x-4">
          <button class="p-2 rounded-full hover:bg-blue-100 transition-colors" @click="showHelp = true">
            <question-circle-icon class="w-5 h-5 text-blue-600" />
          </button>
          <button class="p-2 rounded-full hover:bg-blue-100 transition-colors">
            <settings-icon class="w-5 h-5 text-blue-600" />
          </button>
          <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center">
            <span class="text-sm font-medium text-blue-700">JD</span>
          </div>
        </div>
      </div>
    </header>

    <!-- 主内容区 -->
    <main class="flex-1 flex overflow-hidden">
      <!-- 左侧面板 - 历史记录 -->
      <aside class="w-64 bg-white border-r border-blue-100 flex flex-col hidden md:flex">
        <div class="p-4 border-b border-blue-100">
          <h2 class="font-semibold text-blue-800">历史记录</h2>
        </div>
        <div class="flex-1 overflow-y-auto p-2">
          <div 
            v-for="(item, index) in history" 
            :key="index"
            class="p-3 rounded-md hover:bg-blue-50 cursor-pointer mb-1 transition-colors"
            :class="{ 'bg-blue-100': index === activeHistory }"
            @click="loadHistory(index)"
          >
            <p class="text-sm truncate">{{ item.prompt.substring(0, 30) }}...</p>
            <p class="text-xs text-blue-500 mt-1">{{ formatTime(item.timestamp) }}</p>
          </div>
        </div>
        <div class="p-4 border-t border-blue-100">
          <button class="text-sm text-blue-600 hover:text-blue-700 transition-colors flex items-center">
            <folder-open-icon class="w-4 h-4 mr-2" />
            打开保存的代码
          </button>
        </div>
      </aside>

      <!-- 中间区域 - 输入输出 -->
      <div class="flex-1 flex flex-col overflow-hidden">
        <!-- 输入区域 -->
        <div class="flex-1 flex flex-col border-b border-blue-100 overflow-hidden">
          <div class="bg-white p-3 flex justify-between items-center">
            <h2 class="font-medium text-blue-800">提示词</h2>
            <div class="flex space-x-2">
              <select class="bg-blue-50 text-sm rounded px-2 py-1 border border-blue-200 focus:outline-none focus:ring-1 focus:ring-blue-400" v-model="selectedLanguage">
                <option value="javascript">JavaScript</option>
                <option value="python">Python</option>
                <option value="java">Java</option>
                <option value="go">Go</option>
                <option value="vue">Vue</option>
                <option value="react">React</option>
              </select>
              <button class="bg-blue-50 hover:bg-blue-100 text-sm rounded px-3 py-1 transition-colors border border-blue-200">
                模板
              </button>
            </div>
          </div>
          <div class="flex-1 overflow-hidden flex flex-col">
            <textarea 
              v-model="prompt"
              class="flex-1 bg-blue-50 border-none p-4 focus:outline-none resize-none"
              placeholder="请输入您的代码生成需求，例如：生成一个Vue组件，实现表单验证功能..."
            ></textarea>
            <div class="p-4 border-t border-blue-100 flex justify-between items-center">
              <div class="text-sm text-blue-600">
                提示：更具体的需求会得到更精确的结果
              </div>
              <button 
                @click="generateCode"
                class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-md transition-all transform hover:scale-105 flex items-center shadow-sm"
                :disabled="isGenerating"
              >
                <span v-if="!isGenerating">生成代码</span>
                <span v-if="isGenerating" class="flex items-center">
                  <loading-icon class="w-4 h-4 mr-2 animate-spin" />
                  生成中...
                </span>
                <send-icon class="w-4 h-4 ml-2" />
              </button>
            </div>
          </div>
        </div>

        <!-- 输出区域 -->
        <div class="flex-1 flex flex-col overflow-hidden">
          <div class="bg-white p-3 flex justify-between items-center">
            <h2 class="font-medium text-blue-800">生成结果</h2>
            <div class="flex space-x-2">
              <button 
                @click="copyToClipboard"
                class="bg-blue-50 hover:bg-blue-100 text-sm rounded px-3 py-1 transition-colors flex items-center border border-blue-200"
              >
                <copy-icon class="w-4 h-4 mr-1" />
                复制
              </button>
              <button 
                @click="optimizeCode"
                class="bg-cyan-600 hover:bg-cyan-700 text-white text-sm rounded px-3 py-1 transition-colors flex items-center shadow-sm"
                :disabled="!generatedCode || isOptimizing"
              >
                <span v-if="!isOptimizing">优化代码</span>
                <span v-if="isOptimizing" class="flex items-center">
                  <loading-icon class="w-4 h-4 mr-2 animate-spin" />
                  优化中...
                </span>
              </button>
              <button class="bg-blue-50 hover:bg-blue-100 p-1.5 rounded transition-colors border border-blue-200">
                <download-icon class="w-4 h-4 text-blue-700" />
              </button>
            </div>
          </div>
          <div class="flex-1 overflow-hidden">
            <div v-if="!generatedCode" class="h-full flex items-center justify-center text-blue-400">
              代码将在这里显示...
            </div>
            <div v-else class="h-full overflow-auto">
              <pre class="p-4"><code :class="`language-${selectedLanguage}`">{{ generatedCode }}</code></pre>
            </div>
          </div>
        </div>
      </div>

      <!-- 右侧面板 - 优化建议和使用说明 -->
      <aside class="w-72 bg-white border-l border-blue-100 flex flex-col hidden lg:flex">
        <div class="p-4 border-b border-blue-100 flex justify-between">
          <h2 class="font-semibold text-blue-800">优化建议</h2>
          <button @click="showUsage = !showUsage" class="text-sm text-blue-600 hover:underline">
            {{ showUsage ? '显示建议' : '使用说明' }}
          </button>
        </div>
        <div class="flex-1 overflow-y-auto p-4">
          <!-- 使用说明面板 -->
          <div v-if="showUsage" class="space-y-3 text-sm">
            <div>
              <h3 class="font-medium text-blue-700 mb-1">如何使用系统</h3>
              <ol class="list-decimal list-inside text-gray-700 space-y-2">
                <li>在左侧输入框中描述您的代码需求</li>
                <li>选择目标编程语言（默认JavaScript）</li>
                <li>点击"生成代码"按钮获取AI生成结果</li>
                <li>如需改进，点击"优化代码"获取建议</li>
                <li>可复制代码到剪贴板或下载保存</li>
              </ol>
            </div>
            
            <div>
              <h3 class="font-medium text-blue-700 mb-1">提示词技巧</h3>
              <ul class="list-disc list-inside text-gray-700 space-y-2">
                <li>明确指定编程语言和版本</li>
                <li>描述功能需求和使用场景</li>
                <li>说明特殊要求（如性能、兼容性）</li>
                <li>示例："用Python 3.9写一个高效的JSON解析器，处理大于1GB的文件"</li>
              </ul>
            </div>
            
            <div>
              <h3 class="font-medium text-blue-700 mb-1">代码优化选项</h3>
              <ul class="list-disc list-inside text-gray-700 space-y-2">
                <li>性能优化：提升代码执行效率</li>
                <li>可读性优化：改善代码结构和注释</li>
                <li>安全性优化：修复潜在安全漏洞</li>
                <li>兼容性优化：适配不同环境和版本</li>
              </ul>
            </div>
          </div>
          
          <!-- 优化建议面板 -->
          <div v-else>
            <div v-if="!optimizationSuggestions" class="text-blue-400 text-sm">
              生成代码后将显示优化建议...
            </div>
            <div v-else class="space-y-4">
              <div class="bg-blue-50 p-3 rounded-md">
                <h3 class="font-medium text-blue-700 text-sm">性能优化</h3>
                <ul class="mt-2 text-sm text-gray-700 space-y-1 list-disc list-inside">
                  <li v-for="(item, index) in optimizationSuggestions.performance" :key="index">
                    {{ item }}
                  </li>
                </ul>
              </div>
              
              <div class="bg-blue-50 p-3 rounded-md">
                <h3 class="font-medium text-blue-700 text-sm">代码规范</h3>
                <ul class="mt-2 text-sm text-gray-700 space-y-1 list-disc list-inside">
                  <li v-for="(item, index) in optimizationSuggestions规范" :key="index">
                    {{ item }}
                  </li>
                </ul>
              </div>
              
              <div class="bg-blue-50 p-3 rounded-md">
                <h3 class="font-medium text-amber-700 text-sm">潜在问题</h3>
                <ul class="mt-2 text-sm text-gray-700 space-y-1 list-disc list-inside">
                  <li v-for="(item, index) in optimizationSuggestions.issues" :key="index">
                    {{ item }}
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </aside>
    </main>

    <!-- 底部状态栏 -->
    <footer class="bg-white border-t border-blue-100 py-2 px-4 text-xs text-blue-600 flex justify-between">
      <div>
        <span class="mr-4">模型: GPT-4</span>
        <span>tokens: {{ tokenCount.toFixed(0) }}</span>
      </div>
      <div>
        <button class="hover:text-blue-800 transition-colors mr-4" @click="toggleTheme">
          {{ isLightTheme ? '深色模式' : '浅色模式' }}
        </button>
        <button class="hover:text-blue-800 transition-colors">帮助</button>
      </div>
    </footer>

    <!-- 移动端菜单按钮 -->
    <button class="md:hidden fixed bottom-4 right-4 bg-blue-600 hover:bg-blue-700 w-12 h-12 rounded-full flex items-center justify-center shadow-lg z-10">
      <menu-icon class="w-6 h-6 text-white" />
    </button>

    <!-- 帮助提示框 -->
    <div v-if="showHelp" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
      <div class="bg-white rounded-lg shadow-xl w-full max-w-md p-6">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-lg font-semibold text-blue-800">使用帮助</h3>
          <button @click="showHelp = false" class="text-gray-500 hover:text-gray-700">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>
        <div class="text-sm text-gray-700 space-y-3">
          <p>1. 在提示词区域输入您的代码需求，越详细越好</p>
          <p>2. 选择目标编程语言，系统会生成对应代码</p>
          <p>3. 点击"生成代码"按钮获取结果</p>
          <p>4. 可使用"优化代码"功能改进生成的代码</p>
          <p>5. 历史记录会自动保存您的操作</p>
        </div>
        <button @click="showHelp = false" class="mt-6 w-full bg-blue-600 hover:bg-blue-700 text-white py-2 rounded-md transition-colors">
          我知道了
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { 
  CodeIcon, 
  SendIcon, 
  CopyIcon, 
  DownloadIcon, 
  SettingsIcon, 
  QuestionCircleIcon,
  MenuIcon,
  LoadingIcon,
  FolderOpenIcon
} from '@heroicons/vue/24/outline';

// 导入代码高亮库（实际使用时需要安装）
// import hljs from 'highlight.js';
// import 'highlight.js/styles/github.min.css';

export default {
  components: {
    CodeIcon,
    SendIcon,
    CopyIcon,
    DownloadIcon,
    SettingsIcon,
    QuestionCircleIcon,
    MenuIcon,
    LoadingIcon,
    FolderOpenIcon
  },
  setup() {
    // 状态管理
    const prompt = ref('');
    const generatedCode = ref('');
    const isGenerating = ref(false);
    const isOptimizing = ref(false);
    const tokenCount = ref(0);
    const history = ref([]);
    const activeHistory = ref(-1);
    const optimizationSuggestions = ref(null);
    const selectedLanguage = ref('javascript');
    const showUsage = ref(false);
    const showHelp = ref(false);
    const isLightTheme = ref(true);

    // 生成代码
    const generateCode = async () => {
      if (!prompt.value.trim()) return;
      
      isGenerating.value = true;
      
      // 模拟API调用延迟
      await new Promise(resolve => setTimeout(resolve, 1500));
      
      // 根据选择的语言生成示例代码
      const codeExamples = {
        javascript: `// 生成的JavaScript示例代码
function calculateTotal(items) {
  if (!items || !Array.isArray(items)) {
    throw new Error('Invalid input: items must be an array');
  }
  
  return items.reduce((total, item) => {
    // 检查项目是否有效
    if (item && typeof item.price === 'number' && typeof item.quantity === 'number') {
      return total + (item.price * item.quantity);
    }
    return total;
  }, 0);
}

// 使用示例
const shoppingCart = [
  { id: 1, name: '商品A', price: 29.99, quantity: 2 },
  { id: 2, name: '商品B', price: 19.99, quantity: 1 }
];

const total = calculateTotal(shoppingCart);
console.log(\`总价: \$${total.toFixed(2)}\`);`,
        
        python: `# 生成的Python示例代码
def calculate_total(items):
    if not items or not isinstance(items, list):
        raise ValueError("Invalid input: items must be a list")
    
    total = 0.0
    for item in items:
        # 检查项目是否有效
        if isinstance(item, dict) and 'price' in item and 'quantity' in item:
            if isinstance(item['price'], (int, float)) and isinstance(item['quantity'], (int, float)):
                total += item['price'] * item['quantity']
    
    return total

# 使用示例
shopping_cart = [
    {'id': 1, 'name': '商品A', 'price': 29.99, 'quantity': 2},
    {'id': 2, 'name': '商品B', 'price': 19.99, 'quantity': 1}
]

total = calculate_total(shopping_cart)
print(f"总价: \${total:.2f}")`,
        
        // 其他语言的示例代码...
        default: `// 生成的代码
function helloWorld() {
  console.log("Hello, World!");
}

// 调用函数
helloWorld();`
      };
      
      generatedCode.value = codeExamples[selectedLanguage.value] || codeExamples.default;
      
      // 计算模拟的token数量
      tokenCount.value = prompt.value.length + generatedCode.value.length / 4;
      
      // 添加到历史记录
      history.value.unshift({
        prompt: prompt.value,
        code: generatedCode.value,
        timestamp: new Date(),
        language: selectedLanguage.value
      });
      
      isGenerating.value = false;
    };

    // 优化代码
    const optimizeCode = async () => {
      if (!generatedCode.value) return;
      
      isOptimizing.value = true;
      
      // 模拟API调用延迟
      await new Promise(resolve => setTimeout(resolve, 1200));
      
      // 模拟优化建议
      optimizationSuggestions.value = {
        performance: [
          "可以使用for循环替代reduce方法以提高大数据量下的性能",
          "添加缓存机制缓存计算结果，避免重复计算"
        ],
        规范: [
          "建议添加更详细的注释说明函数功能",
          "变量命名可以更具体，如将total改为cartTotal"
        ],
        issues: [
          "未处理价格或数量为负数的情况",
          "缺少对商品对象结构的验证"
        ]
      };
      
      isOptimizing.value = false;
    };

    // 复制到剪贴板
    const copyToClipboard = () => {
      if (generatedCode.value) {
        navigator.clipboard.writeText(generatedCode.value).then(() => {
          // 可以添加复制成功的提示
          alert('代码已复制到剪贴板');
        });
      }
    };

    // 加载历史记录
    const loadHistory = (index) => {
      const item = history.value[index];
      prompt.value = item.prompt;
      generatedCode.value = item.code;
      selectedLanguage.value = item.language || 'javascript';
      activeHistory.value = index;
    };

    // 格式化时间
    const formatTime = (date) => {
      return new Date(date).toLocaleString();
    };

    // 切换主题
    const toggleTheme = () => {
      isLightTheme.value = !isLightTheme.value;
      // 实际项目中可以在这里切换CSS类或变量
    };

    return {
      prompt,
      generatedCode,
      isGenerating,
      isOptimizing,
      tokenCount,
      history,
      activeHistory,
      optimizationSuggestions,
      selectedLanguage,
      showUsage,
      showHelp,
      isLightTheme,
      generateCode,
      optimizeCode,
      copyToClipboard,
      loadHistory,
      formatTime,
      toggleTheme
    };
  }
};
</script>

<style scoped>
/* 代码高亮样式 - 浅蓝色主题适配 */
pre {
  color: #24292e;
  background: transparent;
  font-family: 'Fira Code', 'SFMono-Regular', Consolas, monospace;
  border-radius: 0.3em;
  overflow-x: auto;
  line-height: 1.5;
}

code {
  color: #24292e;
}

/* 语法高亮颜色 - 浅色主题 */
.language-javascript .comment { color: #6a737d; }
.language-javascript .keyword { color: #d73a49; }
.language-javascript .function { color: #6f42c1; }
.language-javascript .string { color: #032f62; }
.language-javascript .number { color: #005cc5; }
.language-javascript .operator { color: #d73a49; }
.language-javascript .variable { color: #005cc5; }
.language-javascript .property { color: #005cc5; }

/* Python 语法高亮 */
.language-python .comment { color: #6a737d; }
.language-python .keyword { color: #d73a49; }
.language-python .function { color: #6f42c1; }
.language-python .string { color: #032f62; }
.language-python .number { color: #005cc5; }
</style>