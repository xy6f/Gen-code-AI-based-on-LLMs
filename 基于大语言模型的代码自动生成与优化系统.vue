<template>
  <div class="font-inter bg-gray-50 text-gray-800 min-h-screen flex flex-col">
    <!-- 顶部导航栏 -->
    <header class="bg-white shadow-sm sticky top-0 z-50 transition-all duration-300" id="navbar">
      <div class="container mx-auto px-4 py-3 flex items-center justify-between">
        <!-- 品牌标识 -->
        <div class="flex items-center space-x-2">
          <div class="h-10 w-10 rounded rounded-lg bg-gradient-to-br from-primary to-accent flex items-center justify-center">
            <i class="fa fa-code text-white text-xl"></i>
          </div>
          <div>
            <h1 class="text-xl font-bold bg-gradient-to-r from-primary to-accent bg-clip-text text-transparent">CodeGen AI</h1>
            <p class="text-xs text-gray-500 -mt-1">智能代码生成与优化助手</p>
          </div>
        </div>
        
        <!-- 主导航 - 桌面版 -->
        <nav class="hidden md:flex items-center space-x-8">
          <a href="#" class="font-medium text-primary border-b-2 border-primary pb-1">代码生成</a>
          <a href="#" class="font-medium text-gray-600 hover:text-primary transition-colors">代码优化</a>
          <a href="#" class="font-medium text-gray-600 hover:text-primary transition-colors">文档生成</a>
          <a href="#" class="font-medium text-gray-600 hover:text-primary transition-colors">学习资源</a>
        </nav>
        
        <!-- 用户区域 -->
        <div class="flex items-center space-x-4">
          <button class="hidden md:flex items-center justify-center h-9 w-9 rounded-full bg-gray-100 text-gray-600 hover:bg-gray-200 transition-colors">
            <i class="fa fa-bell-o"></i>
          </button>
          <button class="hidden md:flex items-center justify-center h-9 w-9 rounded-full bg-gray-100 text-gray-600 hover:bg-gray-200 transition-colors">
            <i class="fa fa-cog"></i>
          </button>
          <div class="relative">
            <button class="flex items-center space-x-2 focus:outline-none group">
              <img src="https://p3-search.byteimg.com/obj/labis/6d3268851fe103723a27a2eae9d4bf0c" alt="用户头像" class="h-9 w-9 rounded-full object-cover border-2 border-transparent group-hover:border-primary transition-all">
              <span class="hidden md:inline font-medium">chy</span>
              <i class="fa fa-angle-down text-gray-500 text-xs group-hover:text-primary transition-colors"></i>
            </button>
          </div>
          <button class="md:hidden text-gray-600" @click="mobileMenuOpen = !mobileMenuOpen">
            <i class="fa fa-bars text-xl"></i>
          </button>
        </div>
      </div>
      
      <!-- 移动端菜单 -->
      <div class="md:hidden" v-show="mobileMenuOpen">
        <div class="bg-white border-t border-gray-100 absolute w-full left-0 shadow-md">
          <div class="container mx-auto px-4 py-3 flex flex-col space-y-3">
            <a href="#" class="font-medium text-primary py-2 border-l-4 border-primary pl-3">代码生成</a>
            <a href="#" class="font-medium text-gray-600 hover:text-primary py-2 border-l-4 border-transparent hover:border-gray-300 pl-3 transition-colors">代码优化</a>
            <a href="#" class="font-medium text-gray-600 hover:text-primary py-2 border-l-4 border-transparent hover:border-gray-300 pl-3 transition-colors">文档生成</a>
            <a href="#" class="font-medium text-gray-600 hover:text-primary py-2 border-l-4 border-transparent hover:border-gray-300 pl-3 transition-colors">学习资源</a>
            <div class="flex items-center justify-between pt-2 border-t border-gray-100">
              <button class="flex items-center space-x-2 text-gray-600 hover:text-primary transition-colors">
                <i class="fa fa-bell-o"></i>
                <span>通知</span>
              </button>
              <button class="flex items-center space-x-2 text-gray-600 hover:text-primary transition-colors">
                <i class="fa fa-cog"></i>
                <span>设置</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </header>

    <!-- 主内容区 -->
    <main class="flex-1 container mx-auto px-4 py-6 flex flex-col md:flex-row gap-6">
      <!-- 左侧边栏 -->
      <aside class="md:w-64 lg:w-72 flex-shrink-0">
        <div class="bg-white rounded-xl shadow-sm p-4 sticky top-24">
          <h2 class="font-semibold text-lg mb-4 flex items-center">
            <i class="fa fa-lightbulb-o text-warning mr-2"></i>
            代码生成场景
          </h2>
          
          <!-- 场景选择 -->
          <div class="space-y-1 mb-6">
            <button 
              class="scene-btn w-full flex items-center justify-between p-3 rounded-lg"
              :class="activeScene === 'generate' ? 'bg-primary/10 text-primary' : 'hover:bg-gray-100 text-gray-700'"
              @click="setActiveScene('generate', '例如：写一个Python函数，计算两个数的最大公约数，并添加详细注释...')"
            >
              <span>通用代码生成</span>
              <i class="fa fa-check-circle"></i>
            </button>
            <button 
              class="scene-btn w-full flex items-center justify-between p-3 rounded-lg hover:bg-gray-100 text-gray-700 font-medium transition-colors"
              :class="activeScene === 'optimize' ? 'bg-primary/10 text-primary' : 'hover:bg-gray-100 text-gray-700'"
              @click="setActiveScene('optimize', '例如：粘贴需要优化的代码，如Python列表遍历、JavaScript异步逻辑等...')"
            >
              <span>代码优化</span>
              <i class="fa fa-arrow-circle-up"></i>
            </button>
            <button 
              class="scene-btn w-full flex items-center justify-between p-3 rounded-lg hover:bg-gray-100 text-gray-700 font-medium transition-colors"
              :class="activeScene === 'explain' ? 'bg-primary/10 text-primary' : 'hover:bg-gray-100 text-gray-700'"
              @click="setActiveScene('explain', '例如：解释这段代码的功能：def gcd(a,b):while b: a,b=b,a%b;return a')"
            >
              <span>代码解释</span>
              <i class="fa fa-book"></i>
            </button>
            <button 
              class="scene-btn w-full flex items-center justify-between p-3 rounded-lg hover:bg-gray-100 text-gray-700 font-medium transition-colors"
              :class="activeScene === 'test' ? 'bg-primary/10 text-primary' : 'hover:bg-gray-100 text-gray-700'"
              @click="setActiveScene('test', '例如：为这个Python GCD函数生成单元测试，覆盖正常/边界值场景')"
            >
              <span>单元测试生成</span>
              <i class="fa fa-check-square-o"></i>
            </button>
            <button 
              class="scene-btn w-full flex items-center justify-between p-3 rounded-lg hover:bg-gray-100 text-gray-700 font-medium transition-colors"
              :class="activeScene === 'translate' ? 'bg-primary/10 text-primary' : 'hover:bg-gray-100 text-gray-700'"
              @click="setActiveScene('translate', '例如：将这段Python GCD函数翻译成JavaScript，保持功能一致')"
            >
              <span>代码翻译（语言转换）</span>
              <i class="fa fa-exchange"></i>
            </button>
          </div>
          
          <!-- 模型设置 -->
          <div class="mb-6">
            <h3 class="font-medium text-gray-700 mb-3">模型设置</h3>
            
            <div class="mb-4">
              <label class="block text-sm text-gray-600 mb-1">模型选择</label>
              <select class="w-full p-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary/50 focus:border-primary outline-none text-sm">
                <option>CodeGen Pro (默认)</option>
              </select>
            </div>
            
            <div class="mb-4">
              <label class="block text-sm text-gray-600 mb-1">生成长度</label>
              <div class="flex items-center space-x-2">
                <input 
                  id="length-slider" 
                  type="range" 
                  min="50" 
                  max="1000" 
                  v-model="genLength" 
                  class="w-full accent-primary"
                  @input="updateGenLength"
                >
                <span id="length-value" class="text-sm font-medium text-primary w-10 text-right">{{ genLength }}</span>
              </div>
              <div class="flex justify-between text-xs text-gray-500 mt-1">
                <span>短 (50)</span>
                <span>中 (500)</span>
                <span>长 (1000)</span>
              </div>
            </div>
            
            <div class="mb-4">
              <label class="block text-sm text-gray-600 mb-1">创造力 (温度值)</label>
              <div class="flex items-center space-x-2">
                <input 
                  id="temp-slider" 
                  type="range" 
                  min="0" 
                  max="1" 
                  step="0.1" 
                  v-model="temperature" 
                  class="w-full accent-primary"
                  @input="updateTemperature"
                >
                <span id="temp-value" class="text-sm font-medium text-primary w-6 text-right">{{ temperature }}</span>
              </div>
              <div class="flex justify-between text-xs text-gray-500 mt-1">
                <span>精确</span>
                <span>平衡</span>
                <span>创意</span>
              </div>
            </div>
          </div>
          
          <!-- 常用语言 -->
          <div>
            <h3 class="font-medium text-gray-700 mb-3">常用语言</h3>
            <div class="flex flex-wrap gap-2">
              <span 
                class="lang-tag px-3 py-1 bg-gray-100 text-gray-700 rounded-full text-xs hover:bg-gray-200 cursor-pointer transition-colors" 
                v-for="lang in languages" 
                :key="lang.value"
                :data-lang="lang.value"
                @click="selectLanguage(lang.value)"
              >
                {{ lang.name }}
              </span>
            </div>
          </div>
        </div>
      </aside>
      
      <!-- 中间主内容区 -->
      <div class="flex-1">
        <!-- 输入区域 -->
        <div class="bg-white rounded-xl shadow-sm p-4 mb-6">
          <h2 class="font-semibold text-lg mb-4">请输入您的代码需求</h2>
          
          <div class="relative">
            <textarea 
              id="prompt-input" 
              class="w-full h-32 p-4 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary/50 focus:border-primary outline-none resize-none transition-all font-code text-sm" 
              :placeholder="promptPlaceholder"
              v-model="userPrompt"
            ></textarea>
            
            <!-- 输入辅助工具栏 -->
            <div class="flex items-center justify-between mt-3 pt-3 border-t border-gray-100">
              <div class="flex items-center space-x-3">
                <button class="text-gray-500 hover:text-primary transition-colors" title="上传代码文件">
                  <i class="fa fa-upload"></i>
                </button>
                <button class="text-gray-500 hover:text-primary transition-colors" title="插入代码块">
                  <i class="fa fa-code"></i>
                </button>
                <button class="text-gray-500 hover:text-primary transition-colors" title="清除内容" @click="clearInput">
                  <i class="fa fa-eraser"></i>
                </button>
              </div>
              
              <button 
                id="generate-button" 
                class="bg-primary hover:bg-primary/90 text-white px-6 py-2.5 rounded-lg font-medium flex items-center space-x-2 transition-all transform hover:scale-[1.02] active:scale-[0.98]"
                @click="generateCode"
              >
                <span>生成代码</span>
                <i class="fa fa-paper-plane"></i>
              </button>
            </div>
          </div>
          
          <!-- 示例提示 -->
          <div class="mt-4 pt-4 border-t border-gray-100">
            <p class="text-sm text-gray-500 mb-2">示例提示：</p>
            <div class="flex flex-wrap gap-2">
              <button 
                class="example-prompt px-3 py-1 bg-gray-100 text-gray-700 rounded-full text-xs hover:bg-gray-200 cursor-pointer transition-colors"
                v-for="example in examples"
                :key="example.id"
                :data-prompt="example.prompt"
                @click="useExamplePrompt(example.prompt)"
              >
                {{ example.label }}
              </button>
            </div>
          </div>
        </div>
        
        <!-- 输出结果区域 -->
        <div class="bg-white rounded-xl shadow-sm overflow-hidden mb-6">
          <!-- 结果工具栏 -->
          <div class="flex items-center justify-between p-4 border-b border-gray-100">
            <h2 class="font-semibold text-lg">生成结果</h2>
            <div class="flex items-center space-x-2">
              <!-- 复制代码 -->
              <div class="relative">
                <button 
                  id="copy-code" 
                  class="text-gray-500 hover:text-primary p-2 rounded hover:bg-gray-100 transition-colors" 
                  title="复制代码"
                  @click="copyToClipboard"
                >
                  <i class="fa fa-copy"></i>
                </button>
                <span id="copy-toast" class="toast" v-show="showCopyToast">复制成功！</span>
              </div>
              <!-- 下载代码 -->
              <button id="download-code" class="text-gray-500 hover:text-primary p-2 rounded hover:bg-gray-100 transition-colors" title="下载代码">
                <i class="fa fa-download"></i>
              </button>
              <!-- 折叠/展开代码 -->
              <button 
                id="toggle-code" 
                class="text-gray-500 hover:text-primary p-2 rounded hover:bg-gray-100 transition-colors" 
                title="折叠代码"
                @click="toggleCode"
              >
                <i :class="isCodeExpanded ? 'fa fa-chevron-up' : 'fa fa-chevron-down'"></i>
              </button>
              <!-- 全屏查看 -->
              <button class="text-gray-500 hover:text-primary p-2 rounded hover:bg-gray-100 transition-colors" title="全屏查看">
                <i class="fa fa-expand"></i>
              </button>
              <!-- 更多操作 -->
              <div class="relative group">
                <button class="text-gray-500 hover:text-primary p-2 rounded hover:bg-gray-100 transition-colors">
                  <i class="fa fa-ellipsis-v"></i>
                </button>
                <div class="absolute right-0 mt-2 w-48 bg-white rounded-lg shadow-lg py-2 hidden group-hover:block z-10">
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">重新生成</a>
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">导出为文件</a>
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">在新窗口打开</a>
                  <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">反馈结果</a>
                </div>
              </div>
            </div>
          </div>
          
          <!-- 代码结果 -->
          <div class="p-0">
            <!-- 语言选择器 -->
            <div class="bg-gray-50 px-4 py-2 border-b border-gray-100 flex items-center justify-between">
              <div class="flex items-center space-x-2">
                <span class="text-sm font-medium code-lang">{{ selectedLanguage }}</span>
                <span class="text-xs text-gray-500 px-2 py-0.5 bg-green-100 text-green-800 rounded">已优化</span>
              </div>
              <select 
                class="text-xs border-none bg-transparent text-gray-600 focus:outline-none focus:ring-1 focus:ring-primary lang-select"
                v-model="selectedLanguage"
                @change="updateCodeLanguage"
              >
                <option>Python</option>
                <option>JavaScript</option>
                <option>Java</option>
                <option>C++</option>
                <option>Other</option>
              </select>
            </div>
            
            <!-- 代码展示 -->
            <div class="relative">
              <!-- 行号 -->
              <div id="code-lines" class="absolute left-0 top-0 bottom-0 w-12 bg-gray-50 border-r border-gray-100 flex flex-col items-end py-4 pr-3 text-gray-400 text-xs font-code select-none">
                <div v-for="(line, index) in codeLines" :key="index" class="code-line">{{ index + 1 }}</div>
              </div>
              
              <!-- 代码内容 -->
              <pre 
                id="code-content" 
                class="font-code text-sm overflow-x-auto scrollbar-hide pl-16 pr-4 py-4 m-0"
                :class="isCodeExpanded ? '' : 'max-h-32 overflow-y-hidden'"
              >
                <code v-html="formattedCode"></code>
              </pre>
            </div>
          </div>
          
          <!-- 代码说明 -->
          <div class="p-4 border-t border-gray-100 bg-gray-50">
            <h3 class="font-medium text-gray-800 mb-2">代码说明</h3>
            <p class="text-sm text-gray-600 mb-3">
              这个函数使用欧几里得算法（辗转相除法）来计算两个整数的最大公约数（GCD）。算法基于以下原理：两个整数的最大公约数等于其中较小的数和两数相除余数的最大公约数。
            </p>
            <div class="flex items-center space-x-2">
              <button 
                class="text-sm text-primary hover:text-primary/80 font-medium flex items-center" 
                @click="likeCode"
              >
                <i class="fa fa-thumbs-up mr-1"></i> 有用 <span class="ml-1 text-gray-500">({{ likeCount }})</span>
              </button>
              <button class="text-sm text-gray-600 hover:text-gray-800 font-medium flex items-center">
                <i class="fa fa-thumbs-down mr-1"></i> 无用
              </button>
              <button class="text-sm text-gray-600 hover:text-gray-800 font-medium flex items-center ml-auto">
                <i class="fa fa-comment-o mr-1"></i> 改进建议
              </button>
            </div>
          </div>
        </div>
        
        <!-- 优化建议 -->
        <div class="bg-white rounded-xl shadow-sm p-4 mb-6">
          <h2 class="font-semibold text-lg mb-3">代码优化建议</h2>
          
          <div class="space-y-4">
            <div 
              class="p-3 bg-blue-50 border border-blue-100 rounded-lg optimize-suggestion" 
              v-for="suggestion in optimizationSuggestions"
              :key="suggestion.id"
              :data-suggestion="suggestion.id"
            >
              <h3 class="font-medium text-blue-800 flex items-center">
                <i :class="suggestion.icon + ' mr-2'"></i> {{ suggestion.title }}
              </h3>
              <p class="text-sm text-blue-700 mt-1">
                {{ suggestion.description }}
              </p>
              <button 
                class="mt-2 text-xs text-primary hover:text-primary/80 font-medium flex items-center apply-suggestion"
                @click="applySuggestion(suggestion.id)"
              >
                <i class="fa fa-check mr-1"></i> 应用此优化
              </button>
            </div>
          </div>
          
          <button class="mt-4 text-sm text-primary hover:text-primary/80 font-medium flex items-center" @click="applyAllSuggestions">
            <i class="fa fa-magic mr-1"></i> 应用所有优化建议
          </button>
        </div>
        
        <!-- 相关生成 -->
        <div class="bg-white rounded-xl shadow-sm p-4">
          <h2 class="font-semibold text-lg mb-4">相关代码生成</h2>
          
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div 
              class="border border-gray-100 rounded-lg p-3 hover:border-primary/50 hover:shadow-sm transition-all cursor-pointer related-code"
              v-for="related in relatedCodes"
              :key="related.id"
              :data-prompt="related.prompt"
              @click="useRelatedPrompt(related.prompt)"
            >
              <h3 class="font-medium text-gray-800 text-sm">{{ related.title }}</h3>
              <p class="text-xs text-gray-500 mt-1">{{ related.description }}</p>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 右侧边栏 -->
      <aside class="hidden lg:block w-72 flex-shrink-0">
        <div class="bg-white rounded-xl shadow-sm p-4 sticky top-24">
          <div class="flex items-center justify-between mb-4">
            <h2 class="font-semibold text-lg flex items-center">
              <i class="fa fa-history text-gray-500 mr-2"></i>
              历史记录
            </h2>
            <button class="text-gray-400 hover:text-gray-600 text-sm" @click="clearAllHistory">
              清除全部
            </button>
          </div>
          
          <!-- 历史记录列表 -->
          <div class="space-y-3 max-h-[calc(100vh-300px)] overflow-y-auto scrollbar-hide">
            <!-- 当前会话 -->
            <div class="border-l-4 border-primary pl-3 py-1">
              <p class="text-sm font-medium text-gray-800 truncate">计算两个数的最大公约数</p>
              <p class="text-xs text-gray-500 mt-0.5">今天 14:35</p>
            </div>
            
            <!-- 历史会话 -->
            <div 
              class="border-l-4 border-transparent hover:border-gray-300 pl-3 py-1 hover:bg-gray-50 rounded-r transition-colors cursor-pointer flex items-center justify-between history-item"
              v-for="history in historyRecords"
              :key="history.id"
              :data-prompt="history.prompt"
              :data-language="history.language"
              @click="loadHistory(history)"
            >
              <div class="truncate">
                <p class="text-sm font-medium text-gray-700 truncate">{{ history.title }}</p>
                <p class="text-xs text-gray-500 mt-0.5">{{ history.time }}</p>
              </div>
              <button 
                class="delete-history text-gray-400 hover:text-danger p-1 opacity-0 hover:opacity-100 transition-opacity" 
                title="删除记录"
                @click.stop="deleteHistory(history.id)"
              >
                <i class="fa fa-trash-o"></i>
              </button>
            </div>
          </div>
        </div>
      </aside>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 移动端菜单状态
      mobileMenuOpen: false,
      
      // 场景选择
      activeScene: 'generate',
      promptPlaceholder: '例如：写一个Python函数，计算两个数的最大公约数，并添加详细注释...',
      userPrompt: '',
      
      // 模型设置
      genLength: 500,
      temperature: 0.7,
      
      // 语言选择
      languages: [
        { name: 'Python', value: 'Python' },
        { name: 'JavaScript', value: 'JavaScript' },
        { name: 'Java', value: 'Java' },
        { name: 'C++', value: 'C++' },
        { name: 'HTML/CSS', value: 'HTML/CSS' },
        { name: 'Go', value: 'Go' },
        { name: 'PHP', value: 'PHP' },
        { name: 'More', value: 'More' }
      ],
      selectedLanguage: 'Python',
      
      // 示例提示
      examples: [
        {
          id: 1,
          label: '用JavaScript写一个防抖函数',
          prompt: '用JavaScript写一个防抖函数，处理按钮重复点击问题，延迟时间可配置'
        },
        {
          id: 2,
          label: '优化这段Python代码的性能',
          prompt: '优化这段Python代码的性能：def sum_list(lst): result=0;for i in range(len(lst)): result+=lst[i];return result'
        },
        {
          id: 3,
          label: '解释这段Java代码的功能',
          prompt: '解释这段Java代码的功能：public static int factorial(int n) {if(n<=1)return 1;return n*factorial(n-1);}'
        }
      ],
      
      // 代码展示
      codeContent: `def gcd(a, b):
    # 确保a和b都是非负整数
    a = abs(a)
    b = abs(b)
    
    # 欧几里得算法：如果b是0，返回a
    while b != 0:
        # 临时存储b的值
        temp = b
        # 计算a除以b的余数，赋值给b
        b = a % b
        # 将原来的b值赋给a
        a = temp
    
    # 当循环结束时，a就是最大公约数
    return a

# 示例用法
if __name__ == "__main__":
    print(gcd(48, 18))  # 输出：6`,
    formattedCode: '',
    codeLines: [],
    isCodeExpanded: true,
    showCopyToast: false,
    
    // 评价计数
    likeCount: 128,
    
    // 优化建议
    optimizationSuggestions: [
      {
        id: 'algorithm',
        title: '算法优化',
        icon: 'fa fa-lightbulb-o',
        description: '对于非常大的数字，可以考虑使用二进制GCD算法，它使用位移操作代替取模运算，在某些情况下性能更好。'
      },
      {
        id: 'type-check',
        title: '类型检查',
        icon: 'fa fa-check-circle',
        description: '可以添加输入类型检查，确保函数只接受整数输入，并在接收非整数时抛出适当的异常信息。'
      },
      {
        id: 'batch-calc',
        title: '批量计算',
        icon: 'fa fa-cubes',
        description: '如果需要计算多个数的最大公约数，可以扩展函数以支持可变参数，通过迭代计算所有数的GCD。'
      }
    ],
    
    // 相关代码生成
    relatedCodes: [
      {
        id: 1,
        title: '最小公倍数（LCM）计算函数',
        description: '与最大公约数相关的常用数学函数',
        prompt: '写一个Python函数，计算两个数的最小公倍数（LCM），基于GCD函数实现'
      },
      {
        id: 2,
        title: '素数检查函数',
        description: '判断一个数是否为素数的高效实现',
        prompt: '写一个Python函数，判断一个数是否为素数，优化判断效率'
      },
      {
        id: 3,
        title: '质因数分解函数',
        description: '将一个数分解为其质因数的乘积',
        prompt: '写一个Python函数，对一个正整数进行质因数分解，返回所有质因数的列表'
      },
      {
        id: 4,
        title: '扩展欧几里得算法',
        description: '不仅计算GCD，还能找到贝祖等式的系数',
        prompt: '实现扩展欧几里得算法的Python函数，返回GCD及贝祖等式的系数x和y'
      }
    ],
    
    // 历史记录
    historyRecords: [
      {
        id: 1,
        title: 'JavaScript防抖函数实现',
        prompt: '用JavaScript写一个防抖函数，处理按钮重复点击问题，延迟时间可配置',
        language: 'JavaScript',
        time: '今天 13:20'
      },
      {
        id: 2,
        title: '优化Python列表处理性能',
        prompt: '优化这段Python代码的性能：def sum_list(lst): result=0;for i in range(len(lst)): result+=lst[i];return result',
        language: 'Python',
        time: '昨天 16:45'
      },
      {
        id: 3,
        title: '解释Java线程池实现代码',
        prompt: '解释这段Java代码的功能：public static int factorial(int n) {if(n<=1)return 1;return n*factorial(n-1);}',
        language: 'Java',
        time: '昨天 10:15'
      },
      {
        id: 4,
        title: 'React组件状态管理最佳实践',
        prompt: 'React组件状态管理最佳实践，对比useState、useReducer和Redux的使用场景',
        language: 'JavaScript',
        time: '2025-06-12'
      }
    ]
  },
  mounted() {
    // 初始化代码行号和格式化
    this.updateCodeLines();
    this.formatCode();
    
    // 监听滚动事件，改变导航栏样式
    window.addEventListener('scroll', this.handleScroll);
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll);
  },
  methods: {
    // 设置活跃场景
    setActiveScene(scene, placeholder) {
      this.activeScene = scene;
      this.promptPlaceholder = placeholder;
    },
    
    // 更新生成长度
    updateGenLength() {
      // 可以添加验证逻辑
    },
    
    // 更新温度值
    updateTemperature() {
      // 可以添加验证逻辑
    },
    
    // 选择编程语言
    selectLanguage(lang) {
      this.selectedLanguage = lang;
      this.updateCodeLanguage();
    },
    
    // 清除输入
    clearInput() {
      this.userPrompt = '';
    },
    
    // 使用示例提示
    useExamplePrompt(prompt) {
      this.userPrompt = prompt;
    },
    
    // 生成代码
    generateCode() {
      // 这里应该有调用API生成代码的逻辑
      console.log('生成代码:', this.userPrompt);
      // 模拟生成成功后的操作
      this.addToHistory();
    },
    
    // 更新代码语言
    updateCodeLanguage() {
      // 实际应用中应该根据语言重新格式化代码
      console.log('代码语言已更新为:', this.selectedLanguage);
    },
    
    // 更新代码行号
    updateCodeLines() {
      this.codeLines = this.codeContent.split('\n');
    },
    
    // 格式化代码（简化版）
    formatCode() {
      // 实际应用中应该使用语法高亮库
      this.formattedCode = this.codeContent
        .replace(/(def|while|return|if|else|for|in|print|import|from|class)/g, '<span class="text-blue-600">$1</span>')
        .replace(/(\w+)\(/g, '<span class="text-green-600">$1</span>(')
        .replace(/#.*/g, '<span class="text-gray-500">$&</span>')
        .replace(/(".*?"|'.*?')/g, '<span class="text-red-600">$1</span>')
        .replace(/(\d+)/g, '<span class="text-orange-600">$1</span>')
        .replace(/(abs|len)/g, '<span class="text-purple-600">$1</span>');
    },
    
    // 复制到剪贴板
    copyToClipboard() {
      navigator.clipboard.writeText(this.codeContent).then(() => {
        this.showCopyToast = true;
        setTimeout(() => {
          this.showCopyToast = false;
        }, 2000);
      });
    },
    
    // 折叠/展开代码
    toggleCode() {
      this.isCodeExpanded = !this.isCodeExpanded;
    },
    
    // 点赞代码
    likeCode() {
      this.likeCount++;
    },
    
    // 应用优化建议
    applySuggestion(id) {
      console.log('应用优化建议:', id);
      // 实际应用中应该有更新代码的逻辑
    },
    
    // 应用所有优化建议
    applyAllSuggestions() {
      console.log('应用所有优化建议');
      // 实际应用中应该有更新代码的逻辑
    },
    
    // 使用相关提示
    useRelatedPrompt(prompt) {
      this.userPrompt = prompt;
    },
    
    // 加载历史记录
    loadHistory(history) {
      this.userPrompt = history.prompt;
      this.selectedLanguage = history.language;
    },
    
    // 删除历史记录
    deleteHistory(id) {
      this.historyRecords = this.historyRecords.filter(record => record.id !== id);
    },
    
    // 清除所有历史记录
    clearAllHistory() {
      if (confirm('确定要清除所有历史记录吗？')) {
        this.historyRecords = [];
      }
    },
    
    // 添加到历史记录
    addToHistory() {
      const newHistory = {
        id: Date.now(),
        title: this.userPrompt.substring(0, 30) + (this.userPrompt.length > 30 ? '...' : ''),
        prompt: this.userPrompt,
        language: this.selectedLanguage,
        time: '刚刚'
      };
      this.historyRecords.unshift(newHistory);
    },
    
    // 处理滚动事件
    handleScroll() {
      const navbar = document.getElementById('navbar');
      if (window.scrollY > 10) {
        navbar.classList.add('shadow');
      } else {
        navbar.classList.remove('shadow');
      }
    }
  }
}
</script>

<style scoped>
/* 导入外部资源 */
@import url('https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css');

/* 工具类样式 */
.content-auto {
  content-visibility: auto;
}
.scrollbar-hide {
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.scrollbar-hide::-webkit-scrollbar {
  display: none;
}
.text-shadow {
  text-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.transition-all-300 {
  transition: all 300ms ease-in-out;
}
.toast {
  position: absolute;
  top: -8px;
  right: 0;
  background: #1E293B;
  color: white;
  font-size: 0.75rem;
  padding: 0.25rem 0.5rem;
  border-radius: 0.25rem;
  z-index: 10;
}
.code-line {
  margin-bottom: 0.25rem;
}

/* 自定义样式 */
::v-deep .scene-btn {
  font-medium transition-colors;
}

::v-deep .lang-tag {
  cursor: pointer;
  transition-colors;
}

::v-deep .example-prompt {
  cursor: pointer;
  transition-colors;
}

::v-deep .related-code {
  transition-all;
}

::v-deep .history-item {
  transition-colors;
}

::v-deep .delete-history {
  transition-opacity;
}
</style>