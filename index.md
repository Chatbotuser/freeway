<body>
<img src="172.jpg"/>
<!-- Load Facebook SDK for JavaScript -->
<div id="fb-root"></div>
<script>
  window.fbAsyncInit = function() {
    FB.init({
      xfbml            : true,
      version          : 'v3.2'
    });
  };

  (function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = 'https://connect.facebook.net/zh_TW/sdk/xfbml.customerchat.js';
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));</script>

<!-- Your customer chat code -->
<div class="fb-customerchat"
  attribution=setup_tool
  page_id="344174496440510"
  theme_color="#0084ff"
  logged_in_greeting="歡迎來到休息站點餐系統，快來吃點東西緩解開車的疲勞吧~"
  logged_out_greeting="歡迎來到休息站點餐系統，快來吃點東西緩解開車的疲勞吧~">
</div>
<body>
