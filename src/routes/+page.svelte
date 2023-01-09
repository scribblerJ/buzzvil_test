<script>
    import { bind } from "svelte/internal";

    const bz_tracking_id = 'JS성공!'
    const base_url = 'https://ad-py3.buzzvil.com'
    let esi = "";
    let already_sent_postback = false;
    let res;

	let integration_types = [
        {
            type: "WEB",
            path: "/action/pb/cpa/default/pixel.gif"
        },
        {
            type: "SDK",
            path: "/action/pb/cpa/"
        },
        {
            type: "MMP",
            path: "/action/pb/cpa/appsflyer/"
        },
    ];

    let selected_type = integration_types[1];
    // let selected_type = null;


    async function sendJsPostback(){
        let querystring='ds_id='+esi+'&bz_tracking_id='+bz_tracking_id
        let url = base_url+selected_type.path+'?'+querystring
        res = await fetch(url, {
            method : 'GET'
        })
    }

    async function sendSdkPostback(){
        let url = base_url+selected_type.path ;
        let ifa = 'e05d4ad6-4826-491c-b3fc-aa4356e458e8';
        let app_id = esi
        res = await fetch(url, {
            method : 'POST',
            body: JSON.stringify({
                app_id,
                ifa
            })
        })
    }

    async function sendMmpPostback(){
        let url = base_url+selected_type.path ;
        let ifa = 'e05d4ad6-4826-491c-b3fc-aa4356e458e8';
        let app_id = esi
        res = await fetch(url, {
            method : 'POST',
            body: JSON.stringify({
                app_id,
                ifa
            })
        })``
    }



    function handlPostback(){
        // esi 전처리하기 (개행문자 제거)
        if (esi != null && selected_type != null){     
            if (selected_type.type = "WEB"){
                sendJsPostback()
                already_sent_postback = true
            }else if(selected_type.type = "SDK"){
                sendSdkPostback()
                already_sent_postback = true
            }else{
                sendMmpPostback()
            }
        }
    };
    
    

</script>
{#if already_sent_postback}
    <h1>포스트백 송신이 완료되었습니다</h1>
    <h3>버즈빌 연동 ID = {esi}</h3>
    <h3>비즈 트래킹 ID = {bz_tracking_id}</h3>
    <h3>전송 시간 = 현재 시간보셈</h3>
    <h3>{selected_type.type}</h3>
    <br>
    <h3>{res}</h3>
{:else}
    <h2>연동 방식을 선택해주세요</h2>
    <select bind:value={selected_type}>
		{#each integration_types as t}
			<option value={t}>
				{t.type}
			</option>
		{/each}
	</select>
    <h3>{selected_type.type}</h3>
    <h2>버즈빌 연동 ID를 입력해주세요</h2>
    <input bind:value={esi}>
    <!-- <h2>{parms}</h2> -->
    
    <button type="button" on:click={handlPostback}>제출하기</button>
{/if}