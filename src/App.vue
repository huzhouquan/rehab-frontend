<template>
  <div class="container-wrapper">
    <div v-if="showDisclaimer" class="modal-overlay">
      <div class="modal-content">
        <h3 style="margin-top: 0; color: #1a1a1a; text-align: center; font-size: 20px;">⚠️ 免责声明与安全提示</h3>
        <div class="modal-text">
          <p>1. 本网站仅用于日常拉伸状态监测、数据参考及健康提醒，不可替代专业医疗机构的诊断、治疗及医学建议。</p>
          <p>2. 网站所提供的拉伸数据、分析结果及相关提示，仅为辅助参考用途，不具备医学诊断效力。</p>
          <p>3. 运动前请进行充分热身，在安全平整的环境中进行拉伸，以降低运动损伤风险。</p>
          <p>4. 若您存在身体不适或损伤，请务必遵循医生指导，切勿仅凭网站提示自行拉伸。</p>
          <p>5. 因用户未遵医嘱、操作不当所导致的任何人身伤害，本网站不承担法律责任。</p>
          <p style="font-weight: bold; color: #333; margin-top: 15px;">用户点击同意即视为已阅读、理解并同意本声明全部条款。</p>
        </div>
        <div style="text-align: center;">
          <label style="cursor: pointer; display: inline-flex; align-items: center; gap: 8px; font-size: 16px; font-weight: bold; margin-bottom: 10px;">
            <input type="checkbox" v-model="isDisclaimerAgreed" style="width: 20px; height: 20px; cursor: pointer;"> 我已知晓并同意上述条款
          </label>
          <p v-if="showWarning" style="color: #FF3B30; font-size: 14px; margin: 0 0 15px 0;">请先勾选“我已知晓”方可进入系统</p>
          <button class="btn btn-start" @click="closeDisclaimer" style="width: 100%; font-size: 18px; padding: 15px; justify-content: center;">确 定</button>
        </div>
      </div>
    </div>

    <div v-if="showIntro" class="modal-overlay">
      <div class="modal-content">
        <h3 style="margin-top: 0; color: #007AFF; text-align: center; font-size: 22px;">📖 欢迎使用智能康复系统</h3>
        <div class="modal-text" style="font-size: 15px;">
          <p><strong>🏋️‍♂️ AI 拉伸姿态评测</strong></p>
          <ul>
            <li><strong>实时防作弊：</strong>系统会自动检测您的鼻尖与双脚踝，确保全身入框才开始计分。</li>
            <li><strong>双重专注计时：</strong>不仅记录开启系统的“实际时间”，还会精准累加您动作达标时的“有效拉伸时间”。</li>
            <li><strong>数据追踪：</strong>自动记录每次训练的时长与动作，帮您建立康复闭环。</li>
          </ul>
          <p><strong>🎤 黑科技双模控制 (手动 + 声控)</strong></p>
          <ul>
            <li><strong>手动模式：</strong>点击左侧按钮，随时开启或关闭视觉引擎。</li>
            <li><strong>声控模式：</strong>点击“开启语音声控”，直接对电脑喊出 <strong>“打开”</strong> 或 <strong>“关闭”</strong>，即可隔空控制摄像头！</li>
          </ul>
          <p><strong>🎉 趣味互动体验</strong></p>
          <ul>
            <li>坚持拉伸每满 30 秒，系统会触发鼓励弹幕与专属欢呼音效！</li>
          </ul>
        </div>
        <div style="text-align: center;">
          <button class="btn btn-info" @click="showIntro = false" style="width: 100%; font-size: 16px; padding: 12px; font-weight: bold;">我了解啦，开始体验！</button>
        </div>
      </div>
    </div>

    <div v-if="showHistory" class="modal-overlay">
      <div class="modal-content" style="max-width: 750px;">
        <h3 style="margin-top: 0; color: #8E24AA; text-align: center; font-size: 22px;">📊 我的康复训练记录</h3>
        
        <div class="modal-text" style="padding: 0; border: none; background: white;">
          <div v-if="historyRecords.length > 0" style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; padding: 0 10px;">
            <label style="cursor: pointer; display: flex; align-items: center; gap: 8px; color: #555; font-weight: bold; font-size: 15px;">
              <input type="checkbox" v-model="selectAll" style="width: 18px; height: 18px; cursor: pointer;"> 全选
            </label>
            <div>
              <button @click="deleteSelected" :disabled="selectedRecords.length === 0" :style="{ opacity: selectedRecords.length === 0 ? 0.5 : 1, cursor: selectedRecords.length === 0 ? 'not-allowed' : 'pointer' }" style="background: #FFF3E0; border: 1px solid #FFE0B2; color: #E65100; padding: 6px 15px; border-radius: 6px; font-weight: bold; margin-right: 10px; transition: 0.2s;">🗑️ 删除选中</button>
              <button @click="deleteAll" style="background: #FFEbee; border: 1px solid #FFCDD2; color: #C62828; padding: 6px 15px; border-radius: 6px; font-weight: bold; cursor: pointer; transition: 0.2s;">🚨 一键清空</button>
            </div>
          </div>

          <div v-if="historyRecords.length === 0" style="text-align: center; padding: 40px; color: #888; font-size: 16px;">
            暂无训练数据，赶快开始你的第一次拉伸吧！
          </div>
          
          <div v-else style="max-height: 350px; overflow-y: auto;">
            <table style="width: 100%; border-collapse: collapse; text-align: left; font-size: 15px;">
              <thead style="position: sticky; top: 0; background: #f5f5f5; z-index: 1;">
                <tr style="border-bottom: 2px solid #ddd;">
                  <th style="padding: 12px; width: 40px; text-align: center;"></th>
                  <th style="padding: 12px; font-weight: bold; color: #555;">开始时间</th>
                  <th style="padding: 12px; font-weight: bold; color: #555;">动作名称</th>
                  <th style="padding: 12px; font-weight: bold; color: #555;">实际时长</th>
                  <th style="padding: 12px; font-weight: bold; color: #34C759;">有效时长</th>
                  <th style="padding: 12px; font-weight: bold; color: #555;">操作</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="record in historyRecords" :key="record.id" style="border-bottom: 1px solid #eee;">
                  <td style="padding: 12px; text-align: center;">
                    <input type="checkbox" :value="record.id" v-model="selectedRecords" style="width: 16px; height: 16px; cursor: pointer;">
                  </td>
                  <td style="padding: 12px; color: #666;">{{ record.startTime }}</td>
                  <td style="padding: 12px; font-weight: bold;">{{ record.poseName }}</td>
                  <td style="padding: 12px; color: #666;">{{ record.actualTime }}</td>
                  <td style="padding: 12px; color: #34C759; font-weight: bold;">{{ record.effectiveTime }}</td>
                  <td style="padding: 12px;">
                    <button @click="deleteRecord(record.id)" style="background: none; border: none; color: #1976D2; cursor: pointer; font-weight: bold; text-decoration: underline;">删除</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        
        <div style="text-align: center; margin-top: 20px;">
          <button class="btn btn-info" @click="showHistory = false" style="width: 100%; font-size: 16px; padding: 12px; font-weight: bold;">关 闭</button>
        </div>
      </div>
    </div>

    <div v-if="showDashboard" class="modal-overlay">
      <div class="modal-content" style="max-width: 800px;">
        <h3 style="margin-top: 0; color: #1565C0; text-align: center; font-size: 22px;">📈 个人康复数据洞察</h3>
        
        <div class="modal-text" style="padding: 20px; border: none; background: white; display: flex; flex-wrap: wrap; gap: 20px; justify-content: center;">
          <div v-if="historyRecords.length === 0" style="text-align: center; padding: 40px; color: #888; font-size: 16px; width: 100%;">
            暂无训练数据，做几次拉伸再来看看你的专属图表吧！
          </div>
          
          <div v-show="historyRecords.length > 0" ref="barChartRef" style="width: 100%; height: 280px; background: #fafafa; border-radius: 12px; padding-top: 10px;"></div>
          <div v-show="historyRecords.length > 0" ref="pieChartRef" style="width: 100%; height: 280px; background: #fafafa; border-radius: 12px; padding-top: 10px;"></div>
        </div>
        
        <div style="text-align: center; margin-top: 10px;">
          <button class="btn btn-info" @click="showDashboard = false" style="width: 100%; font-size: 16px; padding: 12px; font-weight: bold;">关 闭 看 板</button>
        </div>
      </div>
    </div>

    <div class="app-wrapper">
      
      <div class="sidebar card">
        <h2>⚕️ 智能康复引擎</h2>
        <button class="btn btn-info" @click="showIntro = true" style="margin-bottom: 10px;">📖 查看产品说明书</button>
        <button class="btn" @click="showHistory = true" style="background-color: #F3E5F5; color: #8E24AA; border: 1px solid #E1BEE7; font-weight: bold; margin-bottom: 5px;">📊 查看我的训练记录</button>
        <button class="btn" @click="openDashboard" style="background-color: #E3F2FD; color: #1565C0; border: 1px solid #BBDEFB; font-weight: bold; margin-bottom: 20px;">📈 开启数据看板</button>

        <div style="margin-top: 5px;">
          <div class="section-title">硬件控制</div>
          <button class="btn btn-start" @click="startCamera">▶️ 启动视觉系统</button>
          <button class="btn btn-stop" @click="stopCamera">⏹️ 关闭视觉系统</button>
          <button class="btn" @click="toggleMic" :style="micButtonStyle" style="margin-top: 5px; text-align: center;">
            {{ isVoiceControlEnabled ? '🎙️ 语音声控已开启 (听指令中...)' : '🔇 开启语音声控' }}
          </button>
        </div>

        <div style="margin-top: 15px;">
          <button class="btn" @click="showPoses = !showPoses" style="background-color: #E8F5E9; color: #2E7D32; font-weight: bold; border: 1px solid #C8E6C9;">
            🧘‍♀️ {{ showPoses ? '收起拉伸动作库' : '展开标准拉伸动作' }}
          </button>

          <div v-show="showPoses" style="margin-top: 10px; padding: 15px; background: #fafafa; border-radius: 12px; border: 1px solid #eee;">
            <div class="section-title">动作库列表</div>
            <button class="btn" :class="{ 'active-item': activePoseId === 'pose1' }" @click="setStretchPose('pose1', 'images/pose1.jpg', 180)">双手侧平举</button>
            <button class="btn" :class="{ 'active-item': activePoseId === 'pose2' }" @click="setStretchPose('pose2', 'images/pose2.jpg', 45)">大腿前侧拉伸</button>
            <button class="btn" :class="{ 'active-item': activePoseId === 'pose3' }" @click="setStretchPose('pose3', 'images/pose3.jpg', 180)">肩部前拉</button>
            <button class="btn" :class="{ 'active-item': activePoseId === 'pose4' }" @click="setStretchPose('pose4', 'images/pose4.jpg', 45)">颈部侧拉</button>
            <button class="btn" :class="{ 'active-item': activePoseId === 'pose5' }" @click="setStretchPose('pose5', 'images/pose5.jpg', 160)">小腿拉伸</button>
            <img :src="currentImagePath" class="reference-img" alt="动作图示">
          </div>
        </div>
      </div>

      <div class="main-screen">
        <div class="camera-container">
          <video ref="videoRef" class="input_video" width="1280" height="960"></video>
          <canvas ref="canvasRef" class="output_canvas" width="1280" height="960"></canvas>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted, nextTick } from 'vue';
import * as echarts from 'echarts'; // 🌟 引入图表库

// ================= UI 状态与响应式数据 =================
const showDisclaimer = ref(true);
const showIntro = ref(false);
const showHistory = ref(false);
const showPoses = ref(false); 
const showDashboard = ref(false);

const isDisclaimerAgreed = ref(false);
const showWarning = ref(false);

const activePoseId = ref('pose1');
const currentImagePath = ref('images/pose1.jpg');
const currentTargetAngle = ref(180);

const isVoiceControlEnabled = ref(false);

// ================= 历史记录系统 (连通云端版) =================
const historyRecords = ref([]); // 初始为空，由服务器下发
const selectedRecords = ref([]); 
let currentSessionStartTime = ""; 

const poseNameDict = {
  'pose1': '双手侧平举', 'pose2': '大腿前侧拉伸', 'pose3': '肩部前拉', 'pose4': '颈部侧拉', 'pose5': '小腿拉伸'
};

// 🌟 新增：从后端拉取数据的专属函数
const fetchHistory = async () => {
  try {
    const res = await fetch('https://rehab-backend-jmc3.onrender.com/api/history');
    historyRecords.value = await res.json();
  } catch (error) {
    console.error("连接服务器失败，请确保 server.js 已启动！", error);
  }
};

const selectAll = computed({
  get: () => historyRecords.value.length > 0 && selectedRecords.value.length === historyRecords.value.length,
  set: (val) => {
    selectedRecords.value = val ? historyRecords.value.map(r => r.id) : [];
  }
});

// 🌟 修改：发请求给后端删除单条
const deleteRecord = async (id) => {
  if (window.confirm("确定要删除这条训练记录吗？")) {
    await fetch(`https://rehab-backend-jmc3.onrender.com/api/history/${id}`, { method: 'DELETE' });
    selectedRecords.value = selectedRecords.value.filter(selectedId => selectedId !== id);
    await fetchHistory(); // 删完重新向服务器要最新数据
  }
};

// 🌟 修改：发请求给后端批量删除
const deleteSelected = async () => {
  if (window.confirm(`确定要删除选中的 ${selectedRecords.value.length} 条记录吗？`)) {
    await fetch('https://rehab-backend-jmc3.onrender.com/api/history/batch', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ ids: selectedRecords.value })
    });
    selectedRecords.value = [];
    await fetchHistory();
  }
};

// 🌟 修改：发请求给后端一键清空
const deleteAll = async () => {
  if (window.confirm("🚨 警告：确定要清空所有训练记录吗？此操作不可恢复！")) {
    await fetch('https://rehab-backend-jmc3.onrender.com/api/history/all', { method: 'DELETE' });
    selectedRecords.value = [];
    await fetchHistory();
  }
};

// ================= 数据看板 (ECharts) 逻辑 =================
const barChartRef = ref(null);
const pieChartRef = ref(null);

const timeToSeconds = (timeStr) => {
  const [m, s] = timeStr.split(':').map(Number);
  return m * 60 + s;
};

const openDashboard = async () => {
  showDashboard.value = true;
  await nextTick(); 

  if (historyRecords.value.length === 0) return; 

  if (barChartRef.value) {
    const myBarChart = echarts.init(barChartRef.value);
    const recentData = historyRecords.value.slice(0, 7).reverse(); 
    
    myBarChart.setOption({
      title: { text: '最近7次有效拉伸 (秒)', left: 'center', textStyle: { color: '#555', fontSize: 15 } },
      tooltip: { trigger: 'axis' },
      xAxis: { type: 'category', data: recentData.map(r => r.startTime.split(' ')[0]) }, 
      yAxis: { type: 'value' },
      series: [{ 
        data: recentData.map(r => timeToSeconds(r.effectiveTime)), 
        type: 'bar', 
        itemStyle: { color: '#34C759', borderRadius: [4, 4, 0, 0] }, 
        barWidth: '40%'
      }]
    });
  }

  if (pieChartRef.value) {
    const myPieChart = echarts.init(pieChartRef.value);
    const poseCounts = {};
    historyRecords.value.forEach(r => { poseCounts[r.poseName] = (poseCounts[r.poseName] || 0) + 1; });
    const pieData = Object.keys(poseCounts).map(key => ({ name: key, value: poseCounts[key] }));

    myPieChart.setOption({
      title: { text: '拉伸部位偏好', left: 'center', textStyle: { color: '#555', fontSize: 15 } },
      tooltip: { trigger: 'item' },
      series: [{
        type: 'pie', radius: ['40%', '70%'], 
        avoidLabelOverlap: false,
        itemStyle: { borderRadius: 10, borderColor: '#fff', borderWidth: 2 },
        label: { show: false, position: 'center' },
        emphasis: { label: { show: true, fontSize: 16, fontWeight: 'bold' } },
        data: pieData
      }]
    });
  }
};

// ================= 业务与 AI 状态变量 =================
const videoRef = ref(null);
const canvasRef = ref(null);
let canvasCtx = null;
let poseEngine = null;
let cameraEngine = null;

let lastStretchState = "等待检测...";  
let lastStretchColor = "#888";      
let actualSeconds = 0;           
let effectiveSeconds = 0;        
let isPoseStandard = false;      
let timerInterval = null;        

let danmakuStartTime = 0;        
let danmakuMessage = "";         
let cheerAudio = null; 

let speechRecognition = null;

// ================= 逻辑函数 =================
const closeDisclaimer = () => {
  if (isDisclaimerAgreed.value) {
    showDisclaimer.value = false;
    showIntro.value = true;
  } else {
    showWarning.value = true;
  }
};

const setStretchPose = (poseId, imagePath, targetAngle) => {
  activePoseId.value = poseId;
  currentImagePath.value = imagePath;
  currentTargetAngle.value = targetAngle;
  
  lastStretchState = "等待检测...";
  lastStretchColor = "#888";
  actualSeconds = 0; 
  effectiveSeconds = 0; 
};

const formatTime = (sec) => {
  let m = Math.floor(sec / 60).toString().padStart(2, '0');
  let s = (sec % 60).toString().padStart(2, '0');
  return m + ":" + s;
};

const calculateAngle = (a, b, c) => {
  let radians = Math.atan2(c.y - b.y, c.x - b.x) - Math.atan2(a.y - b.y, a.x - b.x);
  let angle = Math.abs(radians * 180.0 / Math.PI);
  if (angle > 180.0) angle = 360.0 - angle; 
  return angle;
};

// ================= AI 视觉处理核心 =================
const onResults = (results) => {
  if (!canvasCtx || !canvasRef.value) return;
  
  canvasCtx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height);
  canvasCtx.drawImage(results.image, 0, 0, canvasRef.value.width, canvasRef.value.height);
  
  if (results.poseLandmarks) {
    window.drawConnectors(canvasCtx, results.poseLandmarks, window.POSE_CONNECTIONS, {color: '#00FF00', lineWidth: 4});
    window.drawLandmarks(canvasCtx, results.poseLandmarks, {color: '#FF0000', lineWidth: 2});

    let p = results.poseLandmarks;
    let lShoulder = p[11], lElbow = p[13], lWrist = p[15];
    let lHip = p[23], lKnee = p[25], lAnkle = p[27];
    let nose = p[0], rAnkle = p[28];

    canvasCtx.save();
    canvasCtx.scale(-1, 1);
    canvasCtx.translate(-canvasRef.value.width, 0);
    canvasCtx.font = "bold 32px -apple-system, sans-serif";

    let isWholeBodyVisible = (nose && lAnkle && rAnkle && 
                              nose.visibility > 0.6 && lAnkle.visibility > 0.6 && rAnkle.visibility > 0.6);

    if (!isWholeBodyVisible) {
        isPoseStandard = false;
        canvasCtx.fillStyle = "rgba(0,0,0,0.7)";
        canvasCtx.fillRect(0, canvasRef.value.height/2 - 60, canvasRef.value.width, 100);
        canvasCtx.fillStyle = "#FFCC00"; 
        canvasCtx.font = "bold 45px -apple-system, sans-serif";
        canvasCtx.fillText("⚠️ 请往后站一点或调整屏幕，露出全身！", canvasRef.value.width/2 - 380, canvasRef.value.height/2 + 10);
    } else {
        if (lShoulder && lElbow && lWrist && lHip && lKnee) {
            let currentAngle = (currentTargetAngle.value === 180) ? calculateAngle(lShoulder, lElbow, lWrist) : calculateAngle(lHip, lKnee, lAnkle); 
            let diff = Math.abs(currentAngle - currentTargetAngle.value);

            if (diff > 35) { lastStretchState = "❌ 动作大幅度偏离"; lastStretchColor = "#FF3B30"; } 
            else if (diff < 20) { lastStretchState = "✅ 动作标准"; lastStretchColor = "#34C759"; }
            
            isPoseStandard = (lastStretchState === "✅ 动作标准");

            canvasCtx.fillStyle = "rgba(0,0,0,0.5)";
            canvasCtx.fillRect(10, 15, canvasCtx.measureText(lastStretchState).width + 20, 50);
            canvasCtx.fillStyle = lastStretchColor;
            canvasCtx.fillText(lastStretchState, 20, 50);
        }
    }

    let timerText = `⏱️ 实际: ${formatTime(actualSeconds)} | 有效: ${formatTime(effectiveSeconds)}`;
    let tWidth = canvasCtx.measureText(timerText).width;
    canvasCtx.fillStyle = "rgba(0,0,0,0.6)";
    canvasCtx.fillRect(canvasRef.value.width - tWidth - 30, 15, tWidth + 20, 50);
    canvasCtx.fillStyle = isPoseStandard ? "#34C759" : "white";
    canvasCtx.fillText(timerText, canvasRef.value.width - tWidth - 20, 50);

    let now = Date.now();
    if (now - danmakuStartTime < 4000) {
        let progress = (now - danmakuStartTime) / 4000;
        let xPos = canvasRef.value.width - (canvasRef.value.width + 500) * progress; 
        canvasCtx.fillStyle = "#FFCC00"; 
        canvasCtx.font = "bold 45px -apple-system, sans-serif";
        canvasCtx.shadowColor = "rgba(0,0,0,0.8)"; canvasCtx.shadowBlur = 10;
        canvasCtx.fillText(danmakuMessage, xPos, 150);
        canvasCtx.shadowBlur = 0;
    }
    canvasCtx.restore();
  }
};

// ================= 摄像头与记录结算控制 =================
const startCamera = () => {
  if (cameraEngine) {
    cameraEngine.start();
    console.log("摄像头已开启");
    actualSeconds = 0; effectiveSeconds = 0; isPoseStandard = false;
    
    let now = new Date();
    currentSessionStartTime = `${now.getMonth() + 1}月${now.getDate()}日 ${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}`;
    
    if (timerInterval) clearInterval(timerInterval);
    timerInterval = setInterval(() => {
        actualSeconds++; 
        if (isPoseStandard) effectiveSeconds++;
        if (actualSeconds > 0 && actualSeconds % 30 === 0) {
            danmakuMessage = `🎉 太棒了！你已经坚持了 ${actualSeconds} 秒！继续保持！`;
            danmakuStartTime = Date.now(); 
            if(cheerAudio) { cheerAudio.currentTime = 0; cheerAudio.play(); }         
        }
    }, 1000);
  }
};

const stopCamera = () => {
  if (actualSeconds > 0) {
    const newRecord = {
      id: Date.now(),
      startTime: currentSessionStartTime,
      actualTime: formatTime(actualSeconds),
      effectiveTime: formatTime(effectiveSeconds),
      poseName: poseNameDict[activePoseId.value]
    };
    
    // 🌟 将新记录发往云端，保存成功后再拉取最新列表
    fetch('https://rehab-backend-jmc3.onrender.com/api/history', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(newRecord)
    }).then(() => fetchHistory()); 
  }

  if (videoRef.value && videoRef.value.srcObject) { 
    videoRef.value.srcObject.getTracks().forEach(track => track.stop()); 
    videoRef.value.srcObject = null; 
  }
  if (timerInterval) { clearInterval(timerInterval); timerInterval = null; }
  isPoseStandard = false;
  
  if (canvasCtx && canvasRef.value) {
    canvasCtx.clearRect(0, 0, canvasRef.value.width, canvasRef.value.height);
    canvasCtx.save(); canvasCtx.scale(-1, 1); canvasCtx.translate(-canvasRef.value.width, 0);
    canvasCtx.fillStyle = "#8E8E93"; canvasCtx.font = "bold 36px -apple-system, sans-serif";
    canvasCtx.fillText("摄像头已关闭，记录已保存", canvasRef.value.width/2 - 200, canvasRef.value.height/2);
    canvasCtx.restore(); 
  }
};

// ================= AI 语音引擎 =================
const initSpeechControl = () => {
  const SpeechRec = window.SpeechRecognition || window.webkitSpeechRecognition;
  if (SpeechRec) {
      speechRecognition = new SpeechRec();
      speechRecognition.lang = 'zh-CN';    
      speechRecognition.continuous = true; 

      speechRecognition.onresult = function(event) {
          let text = event.results[event.results.length - 1][0].transcript;
          console.log("🎤 听到指令: " + text);
          if (text.includes("打开") || text.includes("启动")) startCamera(); 
          else if (text.includes("关闭") || text.includes("停止")) stopCamera();  
      };

      speechRecognition.onend = function() { 
        if (isVoiceControlEnabled.value) speechRecognition.start(); 
      };
      speechRecognition.onerror = function(event) {
          if (event.error === 'not-allowed') {
              console.log("❌ 麦克风权限被拒");
              isVoiceControlEnabled.value = false;
          }
      };
  } else {
      console.log("❌ 浏览器不支持声控");
  }
};

const toggleMic = () => {
  if (!speechRecognition) { alert("你的浏览器不支持声控哦！建议使用 Edge 或 Chrome。"); return; }
  isVoiceControlEnabled.value = !isVoiceControlEnabled.value; 
  if (isVoiceControlEnabled.value) { 
    try { speechRecognition.start(); } catch(e) {} 
  } else { 
    speechRecognition.stop(); 
  }
};

const micButtonStyle = computed(() => {
  return isVoiceControlEnabled.value 
    ? { backgroundColor: '#E3F2FD', color: '#007AFF', border: '1px solid #BBDEFB' }
    : { backgroundColor: '#f5f5f5', color: '#555', border: 'none' };
});

// ================= Vue 生命周期 =================
onMounted(() => {
  // 🌟 网页刚打开时，立刻向服务器请求历史数据
  fetchHistory();
  canvasCtx = canvasRef.value.getContext('2d');
  cheerAudio = new Audio('/audio/cheer.mp3'); 
  
  poseEngine = new window.Pose({locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/pose/${file}`});
  poseEngine.setOptions({ modelComplexity: 1, smoothLandmarks: true, minDetectionConfidence: 0.5, minTrackingConfidence: 0.5 });
  poseEngine.onResults(onResults);
  
  cameraEngine = new window.Camera(videoRef.value, { 
    onFrame: async () => { await poseEngine.send({image: videoRef.value}); }, 
    width: 1280, 
    height: 960 
  });

  initSpeechControl();
});

onUnmounted(() => {
  stopCamera();
  if (speechRecognition && isVoiceControlEnabled.value) {
    speechRecognition.stop();
  }
});
</script>

<style scoped>
/* ================= 魔法 1：全局锁定 ================= */
:global(body) {
  margin: 0;
  overflow: hidden; 
}

.container-wrapper {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; 
  background-color: #f0f2f5; 
  height: 100vh; 
  width: 100vw;  
  box-sizing: border-box;
  padding: 15px; 
  display: flex; 
  justify-content: center; 
  color: #333;
}

/* ================= 主体框架 (桌面端固定布局) ================= */
.app-wrapper { 
  display: flex; 
  flex-direction: row; 
  width: 100%; 
  max-width: 1600px; 
  height: 100%; 
  gap: 20px; 
}

/* 🟢 左侧控制台：固定宽度，自己可以滚动 */
.sidebar { 
  flex: 0 0 320px; 
  width: 320px; 
  height: 100%; 
  background: white; 
  border-radius: 16px; 
  box-shadow: 0 8px 30px rgba(0,0,0,0.04); 
  padding: 20px; 
  box-sizing: border-box;
  display: flex; 
  flex-direction: column; 
  overflow-y: auto; 
}

/* 🟢 右侧主屏幕：霸占剩余空间，靠左对齐，强制截断越界内容 */
.main-screen { 
  flex: 1; 
  min-width: 0; 
  height: 100%; 
  display: flex; 
  justify-content: flex-start; /* 靠左对齐 */
  align-items: center; 
  background: transparent; 
  padding: 0 20px; 
  box-shadow: none;
  overflow: hidden; 
}

/* ================= 智能缩放的视频框 (靠左) ================= */
.camera-container { 
  position: relative; 
  height: 85%; 
  max-width: 100%;     
  max-height: 100%; 
  aspect-ratio: 4 / 3; 
  background-color: #111; 
  border-radius: 16px; 
  overflow: hidden; 
  box-shadow: 0 20px 50px rgba(0,0,0,0.2);
  margin: 0; /* 取消居中，配合 flex-start 靠左 */
}

.input_video { display: none; } 

.output_canvas { 
  width: 100% !important; 
  height: 100% !important; 
  position: absolute; 
  left: 0; 
  top: 0; 
  transform: scaleX(-1); 
  object-fit: cover; 
} 

/* ================= 基础控件与弹窗样式 ================= */
.modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); backdrop-filter: blur(5px); display: flex; justify-content: center; align-items: center; z-index: 9999; }
.modal-content { background: white; border-radius: 16px; width: 90%; max-width: 600px; padding: 30px; box-shadow: 0 20px 50px rgba(0,0,0,0.3); }
.modal-text { background: #f9f9f9; padding: 15px 20px; border-radius: 10px; color: #555; font-size: 15px; line-height: 1.6; max-height: 350px; overflow-y: auto; margin-bottom: 20px; border: 1px solid #eee; }
.modal-text p { margin-top: 0; margin-bottom: 10px; }
.modal-text ul { padding-left: 20px; margin-top: 5px; }
h2 { margin: 0 0 5px 0; font-size: 20px; color: #1a1a1a; display: flex; align-items: center; gap: 8px;}
.section-title { font-size: 13px; color: #888; text-transform: uppercase; letter-spacing: 1px; margin-bottom: 5px; font-weight: bold;}
.btn { width: 100%; padding: 12px 15px; margin-bottom: 5px; font-size: 15px; font-weight: 600; cursor: pointer; border: none; border-radius: 10px; background-color: #f5f5f5; color: #555; transition: all 0.2s ease; text-align: left;}
.btn:hover { background-color: #e8e8e8; transform: translateY(-1px); }
.btn.active-item { background-color: #34C759; color: white; box-shadow: 0 4px 12px rgba(52, 199, 89, 0.3); }
.btn-start { background-color: #E8F5E9; color: #2E7D32; text-align: center; }
.btn-stop { background-color: #FFEBEE; color: #C62828; text-align: center; }
.btn-info { background-color: #E3F2FD; color: #007AFF; text-align: center; border: 1px solid #BBDEFB; }
.reference-img { width: 100%; height: 300px; object-fit: contain; border-radius: 12px; background-color: #f9f9f9; border: 2px dashed #ddd; margin-top: 10px;}

/* ================= 兼容小屏幕（如手机或极窄窗口）================= */
@media (max-width: 900px) {
  :global(body) { overflow: auto; }
  .container-wrapper { height: auto; display: block; }
  .app-wrapper { flex-direction: column; height: auto; }
  .sidebar { width: 100%; height: auto; overflow: visible; flex: none; }
  .main-screen { height: 60vh; padding: 0; justify-content: center; }
  .camera-container { margin: 0 auto; height: 100%; }
}
</style>