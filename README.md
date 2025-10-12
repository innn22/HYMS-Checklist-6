<안전환경팀>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>트랜스포터(TP) 안전 자율점검</title>
  <style>
    :root{--bg:#f8fafc;--card:#ffffff;--muted:#64748b;--line:#cfd8e3;--ink:#0f172a;--blue:#0ea5e9;--blue-d:#0369a1;--orange:#f59e0b;--orange-d:#c2410c;--lbW:720px;--lbH:540px}
    html,body{height:100%}
    body{margin:0;background:var(--bg);color:var(--ink);font-family:system-ui,Segoe UI,Roboto,Apple SD Gothic Neo,Malgun Gothic,sans-serif}
    .container{max-width:860px;margin:0 auto;padding:12px}
    .head{display:flex;justify-content:space-between;align-items:center;gap:8px;margin-bottom:10px}
    .title{font-size:22px;font-weight:800;margin:0}
    .btns{display:flex;gap:8px;flex-wrap:wrap}
    .btn{padding:8px 10px;border-radius:10px;border:1px solid #d1d5db;background:#fff;cursor:pointer;font-size:14px}
    .card{background:var(--card);border:1px solid var(--line);border-radius:14px;box-shadow:0 4px 14px rgba(0,0,0,.06);padding:12px;margin-bottom:10px}
    .grid2{display:grid;grid-template-columns:1fr 1fr;gap:10px}
    .row{display:grid;grid-template-columns:110px 1fr;gap:8px;align-items:center}
    textarea,input[type="text"],input[type="datetime-local"]{padding:10px;border:1px solid #d1d5db;border-radius:10px;width:100%;box-sizing:border-box;font-size:16px;background:#fff}
    .note{font-size:12px;color:var(--muted)}

    /* 표 레이아웃 */
    .check-table{width:100%;border-collapse:collapse;background:#fff;border-radius:12px;overflow:hidden}
    .check-table th,.check-table td{border:1px solid var(--line);padding:8px;vertical-align:middle}
    .check-table th{background:#eef3f8;font-weight:800;text-align:center}
    .col-no{width:48px;text-align:center}
    .col-item{width:260px}
    .col-method{width:auto}
    .col-opt{width:70px;text-align:center}
    .itemcell{display:grid;grid-template-columns:110px 1fr;gap:8px;align-items:center}
    .thumb{width:110px;height:82px;border:1px solid var(--line);border-radius:8px;object-fit:contain;object-position:center;background:#fff;cursor:zoom-in;touch-action:manipulation}
    .itemtext{display:flex;flex-direction:column;gap:2px}
    .label{font-weight:700}
    .method{font-size:12px;color:var(--muted);line-height:1.45;white-space:pre-line}
    .opt-btn{display:inline-flex;align-items:center;justify-content:center;padding:6px 10px;border-radius:10px;border:1px solid #d1d5db;background:#fff;font-size:13px;width:100%}
    .ok.selected{background:var(--blue);color:#fff;border-color:var(--blue-d)}
    .bad.selected{background:var(--orange);color:#fff;border-color:var(--orange-d)}
    .table-wrap{overflow-x:auto}
    .check-table{min-width:720px}

    /* 대표 사진(고정) */
    .hero-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:8px}
    .hero-title{font-weight:800}
    .hero-box{display:flex;align-items:center;justify-content:center;height:260px;border:1px solid var(--line);border-radius:12px;background:#fafafa}
    .hero-img{max-width:100%;max-height:100%;object-fit:contain;border-radius:10px;cursor:zoom-in;touch-action:manipulation}

    /* 라이트박스(확대) */
    .lightbox{position:fixed;inset:0;display:none;align-items:center;justify-content:center;background:rgba(0,0,0,.7);z-index:10000}
    .lightbox.open{display:flex}
    .lb-img{width:min(var(--lbW),92vw);height:min(var(--lbH),86vh);object-fit:contain;border-radius:12px;background:#fff;box-shadow:0 10px 30px rgba(0,0,0,.35)}
    .lb-close{position:absolute;top:14px;right:16px;border:1px solid #e5e7eb;background:#fff;border-radius:10px;padding:6px 10px;cursor:pointer}
    .lb-prev,.lb-next{position:absolute;top:50%;transform:translateY(-50%);border:1px solid #e5e7eb;background:#fff;border-radius:10px;padding:8px 10px;cursor:pointer}
    .lb-prev{left:16px}
    .lb-next{right:16px}
    .lb-caption{position:absolute;bottom:14px;left:50%;transform:translateX(-50%);background:rgba(255,255,255,.92);border:1px solid #e5e7eb;border-radius:10px;padding:6px 10px;font-size:12px;color:#0f172a;max-width:90vw}
  </style>
</head>
<body>
  <div class="container">
    <header class="head">
      <h1 class="title">트랜스포터(TP) 안전 자율점검</h1>
      <div class="btns">
        <span id="diag" class="note" style="display:none"></span>
        <button id="pngBtn" class="btn">🖼 이미지</button>
        <button id="resetBtn" class="btn">↺ 초기화</button>
      </div>
    </header>

    <div id="capture">
      <!-- 기본 정보 -->
      <section class="card">
        <h2 style="font-size:18px;font-weight:700;margin:0 0 8px">기본 정보</h2>
        <div class="grid2">
          <div class="row"><label for="date">점검 일시</label><input id="date" type="datetime-local" /></div>
          <div class="row"><label for="org">소속</label><input id="org" type="text" placeholder="예) 생산1팀" /></div>
          <div class="row"><label for="site" id="siteLabel">점검 장소</label><input id="site" type="text" placeholder="예) 2공장 A라인" /></div>
          <div class="row"><label for="inspector">점검자</label><input id="inspector" type="text" placeholder="성명" /></div>
          <div class="row"><label for="equip">장비 구분</label><input id="equip" type="text" value="트랜스포터(TP)" /></div>
          <div class="row"><label for="equipId">장비 ID/번호</label><input id="equipId" type="text" placeholder="예) TP-01" /></div>
        </div>
      </section>

      <!-- 대표 사진(고정 이미지) -->
      <section class="card">
        <div class="hero-head"><div class="hero-title">대표 사진</div></div>
        <div class="hero-box">
          <img id="heroImg" class="hero-img" alt="대표 사진" />
        </div>
      </section>

      <!-- 표 형태의 자가진단 -->
      <section class="card table-wrap">
        <table class="check-table" id="checkTable">
          <thead>
            <tr>
              <th class="col-no">No</th>
              <th class="col-item">점검항목</th>
              <th class="col-method">점 검 방 법</th>
              <th class="col-opt">양호</th>
              <th class="col-opt">불량</th>
            </tr>
          </thead>
          <tbody id="tblBody"></tbody>
        </table>

        <div class="grid2" style="margin-top:12px">
          <div>
            <div class="note" style="margin-bottom:6px">특이사항/비고</div>
            <textarea rows="5" id="remarks" placeholder="현장 특이사항 또는 사진 번호 기재"></textarea>
          </div>
          <div>
            <div class="note" style="margin-bottom:6px">조치 내용 (불량 발생 시 필수)</div>
            <textarea rows="5" id="action" placeholder="조치계획·완료일·담당자 등"></textarea>
          </div>
        </div>
      </section>
    </div>

    <div class="note" style="margin-top:12px">URL 예: <code>?equipId=TP-01&site=제1공장A라인&org=생산1팀&equip=트랜스포터(TP)&siteLabel=설치장소</code></div>
  </div>

  <!-- 캡처용 오버레이 -->
  <div id="overlay" class="overlay" style="position:fixed;inset:0;background:rgba(255,255,255,.75);backdrop-filter:saturate(140%) blur(1px);display:none;align-items:center;justify-content:center;z-index:9000;font-weight:700"><div class="spinner" style="width:28px;height:28px;border:3px solid #94a3b8;border-top-color:transparent;border-radius:50%;animation:spin 1s linear infinite;margin-right:10px"></div><span>이미지 준비 중...</span></div>

  <!-- 라이트박스 -->
  <div id="lightbox" class="lightbox" role="dialog" aria-modal="true" aria-label="이미지 확대보기">
    <img id="lbImg" class="lb-img" alt="미리보기" />
    <button id="lbClose" class="lb-close" aria-label="닫기">닫기 ✕</button>
    <button id="lbPrev" class="lb-prev" aria-label="이전">◀</button>
    <button id="lbNext" class="lb-next" aria-label="다음">▶</button>
    <div id="lbCap" class="lb-caption"></div>
  </div>

  <script>
    const $=(id)=>document.getElementById(id);

    /* 대표 사진 파일명을 실제 파일로 교체하세요 (영문 추천) */
    const HERO_SRC='TP.jpg';

    /* 체크리스트 5항목 */
    const checklistTemplate=[
      { key:'emg_stop',     label:'비상정지장치',       method:'비상정지장치 작동 상태 확인 및 스티커 부착여부 확인할 것',                      refImg:'비상정지.png' },
      { key:'rear_camera',  label:'후방카메라',         method:'후방 감시카메라의 모니터 등 작동에 문제가 없을 것',                            refImg:'후방.png' },
      { key:'mirror_alarm', label:'후사경, 경보음',     method:'후사경은 정상 부착되고 경보음 작동상태 확인할 것',                             refImg:'후사경.png' },
      { key:'lamps',        label:'각종 등화류',        method:'전조ㆍ후미ㆍ방향지시등ㆍ경보등의 기능은 정상 작동될 것',                         refImg:'전조등.png' },
      { key:'etc',          label:'기타사항',           method:'타이어 상태 및 소화기 비치여부 확인할 것',                                     refImg:'소화기.png' }
    ];

    const state={date:'',org:'',site:'',inspector:'',equip:'트랜스포터(TP)',equipId:'',checks:Object.fromEntries(checklistTemplate.map(i=>[i.key,''])),remarks:'',action:''};

    function loadParams(){
      const p=new URLSearchParams(location.search), now=new Date();
      state.date=p.get('date') || new Date(now.getTime()-now.getTimezoneOffset()*60000).toISOString().slice(0,16);
      state.org=p.get('org')||''; state.site=p.get('site')||''; state.inspector=p.get('inspector')||'';
      state.equip=p.get('equip')||'트랜스포터(TP)'; state.equipId=p.get('equipId')||'';
      const lbl=p.get('siteLabel'); if(lbl) $('siteLabel').textContent=lbl;
    }

    function applySelection(key){
      const ok=document.querySelector('button.ok[data-key="'+key+'"]');
      const bad=document.querySelector('button.bad[data-key="'+key+'"]');
      if(!ok||!bad) return;
      ok.classList.toggle('selected', state.checks[key]==='양호');
      bad.classList.toggle('selected', state.checks[key]==='불량');
    }

    function renderTable(){
      $('date').value=state.date; $('org').value=state.org; $('site').value=state.site; $('inspector').value=state.inspector; $('equip').value=state.equip; $('equipId').value=state.equipId;
      const hero=$('heroImg'); hero.src=HERO_SRC; hero.onerror=()=>{ hero.src='data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="800" height="520"><rect width="100%" height="100%" fill="%23f1f5f9"/><text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" font-family="sans-serif" font-size="16" fill="%2399a">대표 사진 로드 실패. 파일 경로/이름을 확인하세요.</text></svg>'; };

      const body=$('tblBody'); body.innerHTML='';
      checklistTemplate.forEach((row,i)=>{
        const tr=document.createElement('tr');
        tr.innerHTML =
          '<td class="col-no">'+(i+1)+'</td>'+
          '<td class="col-item"><div class="itemcell">'+
            '<img class="thumb" src="'+row.refImg+'" alt="'+row.label+'" data-full="'+row.refImg+'">'+
            '<div class="itemtext"><div class="label">'+row.label+'</div></div></div></td>'+
          '<td class="col-method"><div class="method">'+row.method+'</div></td>'+
          '<td class="col-opt"><button class="opt-btn ok" data-key="'+row.key+'" data-val="양호">양호</button></td>'+
          '<td class="col-opt"><button class="opt-btn bad" data-key="'+row.key+'" data-val="불량">불량</button></td>';
        body.appendChild(tr);
      });

      body.addEventListener('click',(e)=>{ const b=e.target.closest('button'); if(!b) return; const key=b.getAttribute('data-key'), val=b.getAttribute('data-val'); state.checks[key]=val; applySelection(key); });

      checklistTemplate.forEach(r=>applySelection(r.key));
      $('remarks').value=state.remarks; $('action').value=state.action;

      buildGallery();
    }

    /* 라이트박스 */
    let gallery=[], gi=0;
    function buildGallery(){
      gallery = Array.from(document.querySelectorAll('#heroImg, .thumb')).map(n=>({ src:n.getAttribute('data-full')||n.src, alt:n.alt||'이미지' }));
      Array.from(document.querySelectorAll('#heroImg, .thumb')).forEach((n,idx)=>{
        n.style.cursor='zoom-in'; n.setAttribute('role','button'); n.setAttribute('tabindex','0');
        const open=e=>{ if(e) e.preventDefault(); openLightbox(idx); };
        n.addEventListener('click', open);
        n.addEventListener('touchend', open, {passive:false});
        n.addEventListener('keydown',(e)=>{ if(e.key==='Enter'||e.key===' '){ e.preventDefault(); open(e); }});
      });
    }
    function openLightbox(index){ gi=index; updateLightbox(); $('lightbox').classList.add('open'); document.body.style.overflow='hidden'; }
    function closeLightbox(){ $('lightbox').classList.remove('open'); document.body.style.overflow=''; }
    function prevImg(){ gi=(gi-1+gallery.length)%gallery.length; updateLightbox(); }
    function nextImg(){ gi=(gi+1)%gallery.length; updateLightbox(); }
    function updateLightbox(){ const it=gallery[gi]; $('lbImg').src=it.src; $('lbImg').alt=it.alt; $('lbCap').textContent=it.alt; }
    (function bindLB(){
      $('lbClose').addEventListener('click', closeLightbox);
      $('lbPrev').addEventListener('click', prevImg);
      $('lbNext').addEventListener('click', nextImg);
      $('lightbox').addEventListener('click',(e)=>{ if(e.target===e.currentTarget) closeLightbox(); });
      document.addEventListener('keydown',(e)=>{ if(!$('lightbox').classList.contains('open')) return; if(e.key==='Escape') closeLightbox(); if(e.key==='ArrowLeft') prevImg(); if(e.key==='ArrowRight') nextImg(); });
    })();

    /* html2canvas & PNG 저장 */
    function ensureHtml2Canvas(cb){
      if(window.html2canvas){ cb(); return; }
      const diag=$('diag'); if(diag){diag.style.display='inline'; diag.textContent='이미지 라이브러리 로딩 중...';}
      const srcs=['https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js','https://unpkg.com/html2canvas@1.4.1/dist/html2canvas.min.js','https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js'];
      (function load(i){ if(i>=srcs.length){ if(diag){diag.textContent='이미지 로딩 실패';} return; }
        const s=document.createElement('script'); s.src=srcs[i];
        s.onload=()=>{ if(diag){diag.textContent=''; diag.style.display='none';} cb(); };
        s.onerror=()=>load(i+1);
        document.head.appendChild(s);
      })(0);
    }
    function showBusy(msg){ const o=$('overlay'); o.style.display='flex'; o.querySelector('span').textContent=msg||'처리 중...'; }
    function hideBusy(){ $('overlay').style.display='none'; }
    function downloadPNG(){
      ensureHtml2Canvas(function(){
        showBusy('이미지 준비 중...');
        const el=$('capture'); const prev=el.style.backgroundColor; el.style.backgroundColor='#ffffff';
        const scale=(window.devicePixelRatio>1)?2:1.5;
        html2canvas(el,{scale:scale,useCORS:true,backgroundColor:'#ffffff'}).then((canvas)=>{
          const data=canvas.toDataURL('image/png');
          const name='TP_자가진단_'+(($('equipId').value)||'장비')+'_'+(($('date').value)||'').replace(/[:T]/g,'-')+'.png';
          const a=document.createElement('a'); a.href=data; a.download=name;
          if(typeof a.download==='undefined'){ window.open(data,'_blank'); } else { document.body.appendChild(a); a.click(); a.remove(); }
        }).catch((e)=>alert('이미지 생성 실패: '+e.message)).finally(()=>{ el.style.backgroundColor=prev||''; hideBusy(); });
      });
    }

    function resetForm(){
      const now=new Date();
      state.date=new Date(now.getTime()-now.getTimezoneOffset()*60000).toISOString().slice(0,16);
      state.org=state.site=state.inspector=state.equipId='';
      state.equip='트랜스포터(TP)';
      state.checks=Object.fromEntries(checklistTemplate.map(i=>[i.key,'']));
      state.remarks=state.action='';
      renderTable();
    }

    function bindInputs(){
      $('date').addEventListener('change', e=>state.date=e.target.value);
      $('org').addEventListener('input', e=>state.org=e.target.value);
      $('site').addEventListener('input', e=>state.site=e.target.value);
      $('inspector').addEventListener('input', e=>state.inspector=e.target.value);
      $('equip').addEventListener('input', e=>state.equip=e.target.value);
      $('equipId').addEventListener('input', e=>state.equipId=e.target.value);
      $('remarks').addEventListener('input', e=>state.remarks=e.target.value);
      $('action').addEventListener('input', e=>state.action=e.target.value);
      $('resetBtn').addEventListener('click', resetForm);
      $('pngBtn').addEventListener('click', downloadPNG);
    }

    function start(){ loadParams(); renderTable(); bindInputs(); ensureHtml2Canvas(()=>{}); }
    if(document.readyState==='loading') document.addEventListener('DOMContentLoaded', start); else start();

    /* 간단 테스트 */
    (function(){ try{
      setTimeout(()=>{
        console.assert(document.querySelectorAll('#tblBody tr').length===5,'행 수가 5이 아님');
        console.assert(!!document.getElementById('heroImg'),'대표 사진 요소 누락');
        console.assert(typeof downloadPNG==='function','PNG 저장 함수 누락');
        const labels=[...document.querySelectorAll('#tblBody .label')].map(n=>n.textContent.trim());
        console.assert(labels[0].includes('비상정지장치'),'1번 라벨 불일치');
        console.assert(labels[labels.length-1].includes('기타사항'),'마지막 라벨 불일치');
      },0);
    }catch(e){} })();
  </script>
</body>
</html>
