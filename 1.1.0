import React, { useState, useEffect, useRef } from 'react';

export default function App() {
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  // --- Gemini API 相关状态 ---
  const [chatOpen, setChatOpen] = useState(false);
  const [chatMessages, setChatMessages] = useState([
    { role: 'assistant', text: '你好！我是欧阳轩的专属 AI 助理 ✨。有关商业主持或内容运营的合作，随时问我！' }
  ]);
  const [chatInput, setChatInput] = useState('');
  const [isChatTyping, setIsChatTyping] = useState(false);
  const chatScrollRef = useRef(null);

  const [contactMessage, setContactMessage] = useState('');
  const [isDrafting, setIsDrafting] = useState(false);
  const [isSent, setIsSent] = useState(false);

  const apiKey = ""; // 执行环境将自动提供

  // 自动滚动到聊天底部
  useEffect(() => {
    if (chatScrollRef.current) {
      chatScrollRef.current.scrollTop = chatScrollRef.current.scrollHeight;
    }
  }, [chatMessages, isChatTyping]);

  // 通用的 Gemini API 调用函数 (含指数退避机制)
  const callGemini = async (prompt, sysInstruct) => {
    const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
    const payload = {
      contents: [{ parts: [{ text: prompt }] }],
      systemInstruction: { parts: [{ text: sysInstruct }] }
    };

    let retries = 5;
    let delay = 1000;
    while (retries > 0) {
      try {
        const response = await fetch(url, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });
        if (!response.ok) throw new Error('API Request Failed');
        const data = await response.json();
        return data.candidates?.[0]?.content?.parts?.[0]?.text || "抱歉，AI 暂时无法响应。";
      } catch (error) {
        retries--;
        if (retries === 0) return "网络不稳定，请稍后再试。";
        await new Promise(resolve => setTimeout(resolve, delay));
        delay *= 2; // 指数退避：1s, 2s, 4s, 8s, 16s
      }
    }
  };

  // 处理发送聊天消息
  const handleSendMessage = async (e) => {
    e.preventDefault();
    if (!chatInput.trim() || isChatTyping) return;

    const userText = chatInput.trim();
    setChatMessages(prev => [...prev, { role: 'user', text: userText }]);
    setChatInput('');
    setIsChatTyping(true);

    const sysInstruct = `你是欧阳轩的专属AI助理。欧阳轩是一名资深主持人（擅长政府官方、庆典、媒体活动）以及内容运营专家（B站UP主，0粉起步累计738万+播放量，单支爆款276万+）。他毕业于南昌工学院播音与主持艺术专业，拥有普通话一级乙等及多项AI相关证书。
请以热情、专业、第一人称代言人的口吻回答问题。回答要简短精炼。`;
    const prompt = `访客提问: "${userText}"`;

    const reply = await callGemini(prompt, sysInstruct);
    setChatMessages(prev => [...prev, { role: 'assistant', text: reply }]);
    setIsChatTyping(false);
  };

  // 处理 AI 留言润色
  const handleDraftMessage = async () => {
    setIsDrafting(true);
    const keywords = contactMessage.trim() || "需要一名活动主持人，想聊聊合作细节。";
    
    const sysInstruct = "你是一个专业的商务助理。请根据用户输入的关键词或简略意向，扩写、润色成一段礼貌、专业、得体的商务合作留言，用于发给资深主持人/内容运营专家欧阳轩。只需直接输出留言正文，无需任何问候或解释语。";
    const prompt = `请帮我润色这段留言意向: ${keywords}`;

    const reply = await callGemini(prompt, sysInstruct);
    setContactMessage(reply);
    setIsDrafting(false);
  };
  // --- API 逻辑结束 ---

  const navLinks = [
    { name: '关于我', href: '#about' },
    { name: '经历与成就', href: '#experience' },
    { name: '主持风采', href: '#hosting' },
    { name: '技能与证书', href: '#skills' },
    { name: '联系我', href: '#contact' },
  ];

  return (
    <div className="bg-gray-50 text-gray-800 font-sans min-h-screen selection:bg-blue-200 scroll-smooth pb-20 md:pb-0">
      {/* 导航栏 */}
      <nav className="bg-white shadow-sm fixed w-full z-50 top-0">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex justify-between h-16">
            <div className="flex-shrink-0 flex items-center">
              <a href="#hero" className="text-xl font-bold text-blue-600">欧阳轩</a>
            </div>
            <div className="hidden md:flex space-x-8 items-center">
              {navLinks.map((link) => (
                <a 
                  key={link.name} 
                  href={link.href} 
                  className="hover:text-blue-600 px-3 py-2 rounded-md text-sm font-medium transition-colors text-gray-600"
                >
                  {link.name}
                </a>
              ))}
            </div>
            {/* 移动端菜单按钮 */}
            <div className="md:hidden flex items-center">
              <button 
                type="button" 
                onClick={() => setMobileMenuOpen(!mobileMenuOpen)}
                className="text-gray-500 hover:text-blue-600 focus:outline-none transition-colors" 
                aria-label="Toggle menu"
              >
                <svg className="h-6 w-6" stroke="currentColor" fill="none" viewBox="0 0 24 24">
                  {mobileMenuOpen ? (
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M6 18L18 6M6 6l12 12" />
                  ) : (
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M4 6h16M4 12h16M4 18h16" />
                  )}
                </svg>
              </button>
            </div>
          </div>
        </div>
        {/* 移动端下拉菜单 */}
        {mobileMenuOpen && (
          <div className="md:hidden bg-white border-t border-gray-100 shadow-lg absolute w-full">
            <div className="px-4 pt-2 pb-4 space-y-2">
              {navLinks.map((link) => (
                <a 
                  key={link.name}
                  href={link.href} 
                  onClick={() => setMobileMenuOpen(false)} 
                  className="block px-3 py-2 rounded-md text-base font-medium text-gray-700 hover:text-blue-600 hover:bg-blue-50 transition-colors"
                >
                  {link.name}
                </a>
              ))}
            </div>
          </div>
        )}
      </nav>

      {/* Hero 区域 */}
      <section id="hero" className="pt-28 pb-16 md:pt-40 md:pb-32 bg-gradient-to-br from-blue-50 via-white to-indigo-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex flex-col-reverse md:flex-row items-center">
          <div className="md:w-1/2 mt-10 md:mt-0 md:pr-12 text-center md:text-left">
            <h1 className="text-4xl md:text-5xl lg:text-6xl font-extrabold text-gray-900 tracking-tight leading-tight">
              你好，我是 <br className="hidden md:block" />
              <span className="text-blue-600 mt-2 inline-block">欧阳轩</span>
            </h1>
            <p className="mt-4 text-xl md:text-2xl text-gray-600 font-medium">
              内容运营专家 <span className="text-gray-400 mx-2">|</span> 资深主持人
            </p>
            <p className="mt-6 text-gray-600 text-lg max-w-lg mx-auto md:mx-0 leading-relaxed">
              从百万播放量的B站UP主到专业严谨的舞台主持人，我致力于创造有价值、有影响力的内容体验。
            </p>
            <div className="mt-10 flex flex-col sm:flex-row justify-center md:justify-start space-y-4 sm:space-y-0 sm:space-x-4">
              <a href="#contact" className="inline-flex items-center justify-center px-8 py-3.5 border border-transparent text-base font-medium rounded-full shadow-lg shadow-blue-600/30 text-white bg-blue-600 hover:bg-blue-700 transition-all hover:-translate-y-0.5">
                合作洽谈
              </a>
              <button 
                onClick={() => setChatOpen(true)}
                className="inline-flex items-center justify-center px-8 py-3.5 border-2 border-blue-600 text-base font-medium rounded-full text-blue-600 bg-white hover:bg-blue-50 transition-all shadow-lg"
              >
                ✨ 咨询我的 AI 分身
              </button>
            </div>
          </div>
          <div className="md:w-1/2 flex justify-center relative">
            <div className="absolute inset-0 bg-blue-600/10 blur-3xl rounded-full w-80 h-80 m-auto"></div>
            <img 
              src="4f69c57d6dc50e8ffacd9d4ab1568a15.jpg" 
              alt="欧阳轩" 
              className="relative z-10 w-64 h-64 md:w-80 md:h-80 lg:w-96 lg:h-96 rounded-full object-cover shadow-2xl border-4 border-white transform hover:scale-105 transition-transform duration-500" 
            />
          </div>
        </div>
      </section>

      {/* 关于我 */}
      <section id="about" className="py-20 bg-white">
        <div className="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
          <h2 className="text-3xl font-extrabold text-gray-900 sm:text-4xl">关于我</h2>
          <div className="mt-4 w-20 h-1.5 bg-blue-600 mx-auto rounded-full"></div>
          <p className="mt-8 text-lg text-gray-600 leading-relaxed">
            我是欧阳轩，毕业于南昌工学院播音与主持艺术专业。我是一位拥有敏锐“网感”和极强内容创意策划能力的运营人，同时也是一位风格稳重大气、专业严谨的主持人。我擅长从数据中提炼用户需求，反哺营销策略，实现从“声量曝光”到“交易转化”的业务闭环。
          </p>
          <div className="mt-10 flex flex-wrap justify-center gap-3 md:gap-4">
            <span className="px-5 py-2.5 rounded-full bg-blue-50 text-blue-700 text-sm font-semibold border border-blue-100">播音与主持艺术本科</span>
            <span className="px-5 py-2.5 rounded-full bg-green-50 text-green-700 text-sm font-semibold border border-green-100">普通话一级乙等</span>
            <span className="px-5 py-2.5 rounded-full bg-purple-50 text-purple-700 text-sm font-semibold border border-purple-100">百万粉内容操盘手</span>
            <span className="px-5 py-2.5 rounded-full bg-yellow-50 text-yellow-700 text-sm font-semibold border border-yellow-100">数据驱动营销</span>
          </div>
        </div>
      </section>

      {/* 经历与成就 */}
      <section id="experience" className="py-20 bg-gray-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center mb-16">
            <h2 className="text-3xl font-extrabold text-gray-900 sm:text-4xl">经历与成就</h2>
            <div className="mt-4 w-20 h-1.5 bg-blue-600 mx-auto rounded-full"></div>
          </div>

          <div className="grid grid-cols-1 lg:grid-cols-2 gap-8">
            {/* 运营成就 */}
            <div className="bg-white p-8 md:p-10 rounded-2xl shadow-sm hover:shadow-xl transition-shadow border border-gray-100">
              <div className="flex items-center mb-6">
                <div className="bg-blue-100 p-3.5 rounded-xl text-blue-600 mr-5">
                  <svg xmlns="http://www.w3.org/2000/svg" className="h-7 w-7" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6" />
                  </svg>
                </div>
                <div>
                  <h3 className="text-2xl font-bold text-gray-900">B站自媒体运营核心操盘手</h3>
                  <p className="text-blue-600 text-sm mt-1 font-semibold">2019.07 - 至今</p>
                </div>
              </div>
              <ul className="space-y-4 text-gray-600 leading-relaxed">
                <li className="flex items-start">
                  <span className="text-blue-500 mr-2 mt-1">•</span>
                  <span>零粉起步独立运营，累计斩获超 <span className="font-bold text-blue-600 text-lg">738万+</span> 播放量。</span>
                </li>
                <li className="flex items-start">
                  <span className="text-blue-500 mr-2 mt-1">•</span>
                  <span>打造单支爆款视频，播放量突破 <span className="font-bold text-blue-600 text-lg">276万+</span>。</span>
                </li>
                <li className="flex items-start">
                  <span className="text-blue-500 mr-2 mt-1">•</span>
                  <span>独立负责账号定位、选题策划与数据复盘，通过精细化运营实现高转化。</span>
                </li>
                <li className="flex items-start">
                  <span className="text-blue-500 mr-2 mt-1">•</span>
                  <span>深谙B站、小红书、抖音等主流平台算法机制与用户偏好。</span>
                </li>
              </ul>
            </div>

            {/* 创业与领导经历 */}
            <div className="bg-white p-8 md:p-10 rounded-2xl shadow-sm hover:shadow-xl transition-shadow border border-gray-100">
              <div className="flex items-center mb-6">
                <div className="bg-green-100 p-3.5 rounded-xl text-green-600 mr-5">
                  <svg xmlns="http://www.w3.org/2000/svg" className="h-7 w-7" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z" />
                  </svg>
                </div>
                <h3 className="text-2xl font-bold text-gray-900">项目负责人 & 广播台总监</h3>
              </div>
              
              <div className="mb-8">
                <p className="text-gray-900 font-bold text-lg mb-3">数码设备租赁创业 <span className="text-gray-500 text-sm font-normal bg-gray-100 px-2 py-1 rounded ml-2">项目负责人</span></p>
                <ul className="space-y-2 text-gray-600 leading-relaxed">
                  <li className="flex items-start">
                    <span className="text-green-500 mr-2 mt-1">•</span>
                    <span>跑通“社媒引流-线下交付-售后风控”全链路，累计创收 4.5万元。</span>
                  </li>
                </ul>
              </div>
              
              <div className="pt-6 border-t border-gray-100">
                <p className="text-gray-900 font-bold text-lg mb-3">校园广播台 <span className="text-gray-500 text-sm font-normal bg-gray-100 px-2 py-1 rounded ml-2">总监 | 2022.09 – 2024.06</span></p>
                <ul className="space-y-2 text-gray-600 leading-relaxed">
                  <li className="flex items-start">
                    <span className="text-green-500 mr-2 mt-1">•</span>
                    <span>统筹 30人团队 的日常运营与活动执行。</span>
                  </li>
                  <li className="flex items-start">
                    <span className="text-green-500 mr-2 mt-1">•</span>
                    <span>主导策划并落地 10余场校园大型活动。</span>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </section>

      {/* 主持风采 */}
      <section id="hosting" className="py-20 bg-white">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center mb-16">
            <h2 className="text-3xl font-extrabold text-gray-900 sm:text-4xl">资深主持人</h2>
            <div className="mt-4 w-20 h-1.5 bg-blue-600 mx-auto rounded-full"></div>
            <p className="mt-6 text-lg text-gray-600 max-w-3xl mx-auto leading-relaxed">
              主持风格：落落大方、稳重大气、专业严谨、掌控全局。<br/>以最专业的职业素养对待每一场主持。
            </p>
          </div>
          
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
            <div className="p-8 bg-blue-50/50 rounded-2xl hover:shadow-lg transition-all border border-blue-100 text-center flex flex-col justify-center group">
              <h4 className="text-xl font-bold text-gray-900 mb-4 group-hover:text-blue-600 transition-colors">政府官方类大型活动</h4>
              <p className="text-gray-600 leading-relaxed text-sm">博览会 / 开幕式 / 高峰论坛 / 新闻发布会 / 启动仪式 / 文艺演出</p>
            </div>
            <div className="p-8 bg-purple-50/50 rounded-2xl hover:shadow-lg transition-all border border-purple-100 text-center flex flex-col justify-center group">
              <h4 className="text-xl font-bold text-gray-900 mb-4 group-hover:text-purple-600 transition-colors">庆典会议类活动</h4>
              <p className="text-gray-600 leading-relaxed text-sm">晚会 / 晚宴 / 年会庆典 / 颁奖盛典 / 签约仪式 / 发布会</p>
            </div>
            <div className="p-8 bg-green-50/50 rounded-2xl hover:shadow-lg transition-all border border-green-100 text-center flex flex-col justify-center group">
              <h4 className="text-xl font-bold text-gray-900 mb-4 group-hover:text-green-600 transition-colors">电视台 & 媒体活动</h4>
              <p className="text-gray-600 leading-relaxed text-sm">综艺娱乐 / 节目主持 / 视频拍摄 / 直播互动 / 各类音乐节</p>
            </div>
            <div className="p-8 bg-yellow-50/50 rounded-2xl hover:shadow-lg transition-all border border-yellow-100 text-center flex flex-col justify-center group">
              <h4 className="text-xl font-bold text-gray-900 mb-4 group-hover:text-yellow-600 transition-colors">房产与汽车品牌活动</h4>
              <p className="text-gray-600 leading-relaxed text-sm">开盘仪式 / 暖场活动 / 全国车展 / 上市发布会 / 试乘试驾</p>
            </div>
          </div>
        </div>
      </section>

      {/* 技能与证书 */}
      <section id="skills" className="py-20 bg-gray-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center mb-16">
            <h2 className="text-3xl font-extrabold text-gray-900 sm:text-4xl">技能与证书</h2>
            <div className="mt-4 w-20 h-1.5 bg-blue-600 mx-auto rounded-full"></div>
          </div>
          
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-12 lg:gap-16">
            {/* 专业技能 */}
            <div className="bg-white p-8 md:p-10 rounded-2xl shadow-sm border border-gray-100">
              <h3 className="text-2xl font-bold text-gray-900 mb-8 flex items-center">
                <span className="w-8 h-8 rounded-lg bg-blue-100 text-blue-600 flex items-center justify-center mr-3 text-lg">💡</span>
                核心技能
              </h3>
              <div className="space-y-6">
                <div>
                  <div className="flex justify-between items-center mb-2">
                    <span className="font-semibold text-gray-700">内容策划与创意</span>
                    <span className="text-sm text-blue-600 font-bold">95%</span>
                  </div>
                  <div className="w-full bg-gray-100 rounded-full h-2.5">
                    <div className="bg-blue-600 h-2.5 rounded-full" style={{ width: '95%' }}></div>
                  </div>
                </div>
                <div>
                  <div className="flex justify-between items-center mb-2">
                    <span className="font-semibold text-gray-700">数据分析与复盘</span>
                    <span className="text-sm text-green-600 font-bold">90%</span>
                  </div>
                  <div className="w-full bg-gray-100 rounded-full h-2.5">
                    <div className="bg-green-500 h-2.5 rounded-full" style={{ width: '90%' }}></div>
                  </div>
                </div>
                <div>
                  <div className="flex justify-between items-center mb-2">
                    <span className="font-semibold text-gray-700">视听剪辑工具</span>
                    <span className="text-sm text-purple-600 font-bold">85%</span>
                  </div>
                  <div className="w-full bg-gray-100 rounded-full h-2.5">
                    <div className="bg-purple-500 h-2.5 rounded-full" style={{ width: '85%' }}></div>
                  </div>
                </div>
                <div>
                  <div className="flex justify-between items-center mb-2">
                    <span className="font-semibold text-gray-700">主持与表达能力</span>
                    <span className="text-sm text-yellow-500 font-bold">95%</span>
                  </div>
                  <div className="w-full bg-gray-100 rounded-full h-2.5">
                    <div className="bg-yellow-400 h-2.5 rounded-full" style={{ width: '95%' }}></div>
                  </div>
                </div>
                <div>
                  <div className="flex justify-between items-center mb-2">
                    <span className="font-semibold text-gray-700">团队统筹管理</span>
                    <span className="text-sm text-red-500 font-bold">85%</span>
                  </div>
                  <div className="w-full bg-gray-100 rounded-full h-2.5">
                    <div className="bg-red-400 h-2.5 rounded-full" style={{ width: '85%' }}></div>
                  </div>
                </div>
              </div>
            </div>

            {/* 证书展示 */}
            <div>
              <h3 className="text-2xl font-bold text-gray-900 mb-8 flex items-center">
                <span className="w-8 h-8 rounded-lg bg-yellow-100 text-yellow-600 flex items-center justify-center mr-3 text-lg">🏆</span>
                荣誉证书
              </h3>
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-5">
                <div className="bg-white p-4 rounded-2xl shadow-sm hover:shadow-lg transition-shadow border border-gray-100 flex flex-col items-center group">
                  <div className="w-full h-44 bg-gray-50 flex items-center justify-center rounded-xl overflow-hidden mb-4 p-2">
                     <img src="ai编程证书.jpg" alt="AI编程证书" className="max-w-full max-h-full object-contain group-hover:scale-105 transition-transform duration-300" />
                  </div>
                  <p className="text-center text-sm font-bold text-gray-800">AI编程证书</p>
                </div>
                <div className="bg-white p-4 rounded-2xl shadow-sm hover:shadow-lg transition-shadow border border-gray-100 flex flex-col items-center group">
                  <div className="w-full h-44 bg-gray-50 flex items-center justify-center rounded-xl overflow-hidden mb-4 p-2">
                    <img src="ai人工智能证书.jpg" alt="AI人工智能证书" className="max-w-full max-h-full object-contain group-hover:scale-105 transition-transform duration-300" />
                  </div>
                  <p className="text-center text-sm font-bold text-gray-800">AI人工智能证书</p>
                </div>
                <div className="bg-white p-4 rounded-2xl shadow-sm hover:shadow-lg transition-shadow border border-gray-100 flex flex-col items-center group">
                  <div className="w-full h-44 bg-gray-50 flex items-center justify-center rounded-xl overflow-hidden mb-4 p-2">
                    <img src="人工智能证书.jpg" alt="人工智能证书" className="max-w-full max-h-full object-contain group-hover:scale-105 transition-transform duration-300" />
                  </div>
                  <p className="text-center text-sm font-bold text-gray-800">人工智能证书</p>
                </div>
                <div className="bg-white p-4 rounded-2xl shadow-sm hover:shadow-lg transition-shadow border border-gray-100 flex flex-col items-center justify-center bg-gradient-to-br from-blue-50 to-indigo-50 min-h-[200px]">
                  <div className="text-center space-y-4">
                    <div className="px-4 py-2 bg-white text-blue-700 rounded-lg text-sm font-bold shadow-sm border border-blue-100">
                      🏅 普通话一级乙等
                    </div>
                    <div className="px-4 py-2 bg-white text-green-700 rounded-lg text-sm font-bold shadow-sm border border-green-100">
                      🚗 C1 驾照
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      {/* 联系我 */}
      <section id="contact" className="py-20 bg-white">
        <div className="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="text-center mb-16">
            <h2 className="text-3xl font-extrabold text-gray-900 sm:text-4xl">联系我</h2>
            <div className="mt-4 w-20 h-1.5 bg-blue-600 mx-auto rounded-full"></div>
            <p className="mt-6 text-lg text-gray-600">
              期待与您进行更深入的交流与合作。
            </p>
          </div>
          
          <div className="grid grid-cols-1 md:grid-cols-3 gap-6 md:gap-8 mb-12">
            <a href="mailto:1833517115@qq.com" className="flex flex-col items-center p-8 bg-gray-50 rounded-2xl border border-gray-100 hover:border-blue-300 hover:shadow-lg transition-all group">
              <div className="bg-blue-100 p-5 rounded-full text-blue-600 mb-5 group-hover:scale-110 transition-transform">
                <svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                </svg>
              </div>
              <p className="text-lg font-bold text-gray-900">电子邮箱</p>
              <p className="mt-2 text-gray-600 font-medium">1833517115@qq.com</p>
            </a>
            
            <a href="tel:18807055168" className="flex flex-col items-center p-8 bg-gray-50 rounded-2xl border border-gray-100 hover:border-green-300 hover:shadow-lg transition-all group">
              <div className="bg-green-100 p-5 rounded-full text-green-600 mb-5 group-hover:scale-110 transition-transform">
                <svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M3 5a2 2 0 012-2h3.28a1 1 0 01.948.684l1.498 4.493a1 1 0 01-.502 1.21l-2.257 1.13a11.042 11.042 0 005.516 5.516l1.13-2.257a1 1 0 011.21-.502l4.493 1.498a1 1 0 01.684.949V19a2 2 0 01-2 2h-1C9.716 21 3 14.284 3 6V5z" />
                </svg>
              </div>
              <p className="text-lg font-bold text-gray-900">联系电话</p>
              <p className="mt-2 text-gray-600 font-medium">18807055168</p>
            </a>
            
            <div className="flex flex-col items-center p-8 bg-gray-50 rounded-2xl border border-gray-100 hover:border-purple-300 hover:shadow-lg transition-all group">
              <div className="bg-purple-100 p-5 rounded-full text-purple-600 mb-5 group-hover:scale-110 transition-transform">
                <svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                </svg>
              </div>
              <p className="text-lg font-bold text-gray-900">所在地</p>
              <p className="mt-2 text-gray-600 font-medium">江西南昌</p>
            </div>
          </div>

          {/* ✨ AI 留言交互表单 */}
          <div className="bg-gray-50 p-8 md:p-10 rounded-3xl border border-gray-200 shadow-sm mx-auto max-w-3xl text-left relative overflow-hidden">
            <div className="absolute top-0 right-0 p-4 opacity-10">
               <svg xmlns="http://www.w3.org/2000/svg" className="h-32 w-32 text-blue-500" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M11.3 1.046A12.014 12.014 0 0010 1a11.965 11.965 0 00-9.014 4.095c.575.253 1.2.453 1.85.589A9.972 9.972 0 0110 3c2.61 0 4.985 1.002 6.764 2.641l1.58-1.58A11.964 11.964 0 0011.3 1.046zM10 5a7.973 7.973 0 00-5.38 2.062l1.49 1.49A5.974 5.974 0 0110 7a5.973 5.973 0 014.25 1.76l1.49-1.49A7.974 7.974 0 0010 5zm0 4a3.974 3.974 0 00-2.83 1.17l1.41 1.41A1.974 1.974 0 0110 11a1.974 1.974 0 011.42.58l1.41-1.41A3.974 3.974 0 0010 9z" clipRule="evenodd" />
                </svg>
            </div>
            
            <h3 className="text-2xl font-bold text-gray-900 mb-6">在线留言探讨合作</h3>
            <div className="space-y-6 relative z-10">
              <div className="space-y-2">
                <div className="flex flex-col sm:flex-row sm:justify-between sm:items-end mb-2 gap-2">
                  <label className="text-sm font-semibold text-gray-700">合作需求描述</label>
                  <button
                    type="button"
                    onClick={handleDraftMessage}
                    disabled={isDrafting}
                    className="text-xs flex items-center justify-center gap-1.5 bg-gradient-to-r from-blue-500 to-indigo-500 text-white px-3 py-1.5 rounded-full hover:shadow-md transition-all disabled:opacity-70"
                  >
                    {isDrafting ? (
                      <svg className="animate-spin h-3.5 w-3.5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle><path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
                    ) : (
                      <svg xmlns="http://www.w3.org/2000/svg" className="h-3.5 w-3.5" viewBox="0 0 20 20" fill="currentColor"><path fillRule="evenodd" d="M5 2a1 1 0 011 1v1h1a1 1 0 010 2H6v1a1 1 0 01-2 0V6H3a1 1 0 010-2h1V3a1 1 0 011-1zm0 10a1 1 0 011 1v1h1a1 1 0 110 2H6v1a1 1 0 11-2 0v-1H3a1 1 0 110-2h1v-1a1 1 0 011-1zM12 2a1 1 0 01.967.744L14.146 7.2 17.5 9.134a1 1 0 010 1.732l-3.354 1.935-1.18 4.455a1 1 0 01-1.933 0L9.854 12.8 6.5 10.866a1 1 0 010-1.732l3.354-1.935 1.18-4.455A1 1 0 0112 2z" clipRule="evenodd" /></svg>
                    )}
                    {isDrafting ? 'AI 正在构思...' : '✨ AI 帮我写 / 润色'}
                  </button>
                </div>
                <textarea 
                  rows={5} 
                  value={contactMessage}
                  onChange={(e) => setContactMessage(e.target.value)}
                  placeholder="请输入您的合作意向或直接点击右上角的 ✨ 按钮让 AI 为您一键生成专业邀约..." 
                  className="w-full bg-white border border-gray-300 rounded-xl px-4 py-3 text-gray-800 placeholder:text-gray-400 focus:outline-none focus:ring-2 focus:ring-blue-500/50 focus:border-blue-500 transition-all resize-none shadow-inner"
                ></textarea>
              </div>
              
              {isSent && (
                <div className="p-3 bg-green-50 border border-green-200 text-green-700 rounded-xl text-sm flex items-center justify-center gap-2">
                  <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd" /></svg>
                  发送成功！(演示前端效果)
                </div>
              )}
              
              <button 
                onClick={() => { if(contactMessage) setIsSent(true); }}
                className="w-full sm:w-auto px-8 py-3.5 bg-gray-900 hover:bg-black text-white font-semibold rounded-xl transition-all shadow-md hover:shadow-lg"
              >
                发 送 留 言
              </button>
            </div>
          </div>
        </div>
      </section>

      {/* 页脚 */}
      <footer className="bg-gray-900 py-10 text-center text-gray-400">
        <p className="font-medium text-sm">© {new Date().getFullYear()} 欧阳轩. All rights reserved.</p>
      </footer>

      {/* ✨ AI 智能客服悬浮组件 */}
      <div className="fixed bottom-6 right-6 z-50 flex flex-col items-end">
        {chatOpen && (
          <div className="mb-4 w-[90vw] sm:w-96 bg-white border border-gray-200 rounded-2xl shadow-2xl overflow-hidden flex flex-col h-[450px] animate-in slide-in-from-bottom-5 duration-300">
            {/* 头部 */}
            <div className="bg-gradient-to-r from-blue-600 to-indigo-600 px-4 py-3.5 flex justify-between items-center text-white shadow-md z-10">
              <div className="flex items-center gap-2.5">
                <div className="bg-white/20 p-1.5 rounded-full">
                   <svg xmlns="http://www.w3.org/2000/svg" className="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
                    <path strokeLinecap="round" strokeLinejoin="round" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z" />
                  </svg>
                </div>
                <span className="font-semibold text-sm">欧阳轩的 AI 分身</span>
              </div>
              <button onClick={() => setChatOpen(false)} className="text-white/80 hover:text-white transition-colors bg-white/10 hover:bg-white/20 rounded-full p-1">
                <svg xmlns="http://www.w3.org/2000/svg" className="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}><path strokeLinecap="round" strokeLinejoin="round" d="M6 18L18 6M6 6l12 12" /></svg>
              </button>
            </div>
            
            {/* 消息区 */}
            <div ref={chatScrollRef} className="flex-1 overflow-y-auto p-4 space-y-4 bg-gray-50/50">
              {chatMessages.map((msg, idx) => (
                <div key={idx} className={`flex ${msg.role === 'user' ? 'justify-end' : 'justify-start'}`}>
                  <div className={`max-w-[85%] rounded-2xl px-4 py-2.5 text-sm leading-relaxed shadow-sm ${
                    msg.role === 'user' 
                      ? 'bg-blue-600 text-white rounded-tr-sm' 
                      : 'bg-white text-gray-800 border border-gray-100 rounded-tl-sm'
                  }`}>
                    {msg.text}
                  </div>
                </div>
              ))}
              {isChatTyping && (
                <div className="flex justify-start">
                  <div className="bg-white border border-gray-100 text-gray-400 rounded-2xl rounded-tl-sm px-4 py-3.5 flex gap-1.5 items-center shadow-sm">
                    <span className="w-1.5 h-1.5 bg-blue-400 rounded-full animate-bounce"></span>
                    <span className="w-1.5 h-1.5 bg-blue-400 rounded-full animate-bounce" style={{animationDelay: '0.2s'}}></span>
                    <span className="w-1.5 h-1.5 bg-blue-400 rounded-full animate-bounce" style={{animationDelay: '0.4s'}}></span>
                  </div>
                </div>
              )}
            </div>

            {/* 输入区 */}
            <div className="p-3 bg-white border-t border-gray-100">
              <form onSubmit={handleSendMessage} className="flex gap-2">
                <input
                  type="text"
                  value={chatInput}
                  onChange={(e) => setChatInput(e.target.value)}
                  placeholder="有什么可以帮您？"
                  className="flex-1 bg-gray-50 border border-gray-200 rounded-xl px-3.5 py-2.5 text-sm text-gray-800 placeholder:text-gray-400 focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400 transition-all"
                />
                <button 
                  type="submit" 
                  disabled={isChatTyping || !chatInput.trim()}
                  className="bg-blue-600 hover:bg-blue-700 disabled:bg-gray-200 disabled:text-gray-400 text-white px-3.5 py-2.5 rounded-xl transition-all flex items-center justify-center shadow-sm"
                >
                  <svg xmlns="http://www.w3.org/2000/svg" className="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
                    <path strokeLinecap="round" strokeLinejoin="round" d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8" />
                  </svg>
                </button>
              </form>
            </div>
          </div>
        )}

        {/* 悬浮唤醒按钮 */}
        <button
          onClick={() => setChatOpen(!chatOpen)}
          className={`w-14 h-14 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-700 hover:to-indigo-700 text-white rounded-full flex items-center justify-center shadow-xl shadow-blue-600/30 transition-transform duration-300 ${chatOpen ? 'scale-0 opacity-0 absolute' : 'scale-100 hover:scale-110'}`}
        >
          <svg xmlns="http://www.w3.org/2000/svg" className="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" strokeWidth={2}>
             <path strokeLinecap="round" strokeLinejoin="round" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z" />
          </svg>
        </button>
      </div>
    </div>
  );
}
