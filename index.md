<body>
<img src="172.jpg"/>
            var Botkit =  require（__dirname  +  '/ CoreBot.js '）;
var request =  require（' request '）;
var express =  require（' express '）;
var bodyParser =  require（' body-parser '）;
var crypto =  require（' crypto '）;

功能 Facebookbot（配置）{

    //創建一個核心botkit bot
    var facebook_botkit =  Botkit（configuration || {}）;

    如果（facebook_botkit。配置。require_delivery）{

        facebook_botkit。on（' message_delivered '，function（bot，message）{

            //獲取此消息中的中介列表
            為（VAR米=  0 ; M <  消息。遞送。中頻。長度 ; M ++）{
                var mid =  message。交貨。中 [m];

                //循環瀏覽此機器人正在進行的所有活動對話
                //並將對話中的消息標記為deliver = true
                機器人。findConversation（消息，函數（康沃）{
                    if（convo）{
                        為（VAR小號=  0 ; S <  康沃。發送。長度 ; S ++）{
                            如果（convo。發送 [s] .send_timestamp  <=  消息。交付。水印 ||
                                （康沃。發送 [S]。api_response  &&  康沃。發送 [S]。api_response。中期 ==中旬））{
                                康沃爾。發送 [s]。deliver  =  true ;
                            }
                        }
                    }
                }）;
            }

        }）;

    }




    //自定義機器人定義，將在新連接時使用
    //產卵！
    facebook_botkit。defineBot（function（botkit，config）{

        var bot = {
            輸入： ' fb '，
            botkit ： botkit，
            config ： config || {}，
            話語： botkit。話語，
        };

        機器人。startConversation  =  function（message，cb）{
            botkit。startConversation（this，message，cb）;
        };

        機器人。createConversation  =  function（message，cb）{
            botkit。createConversation（this，message，cb）;
        };


        機器人。send  =  function（message，cb）{

            var facebook_message = {
                收件人： {}，
                消息： 消息。sender_action  ？ undefined  ： {}
            };

            如果（typeof運算（消息。信道）==  '字串'  &&  消息。信道。匹配（/ \ + \ d + \（\ d \ d \ d \）\ d \ d \ d \ - \ d \ d \ d \ d /））{
                facebook_message。收件人。phone_number  =  消息。通道 ;
            } else {
                facebook_message。收件人。id  =  消息。通道 ;
            }

            如果（！消息。sender_action）{
                如果（消息。文本）{
                    facebook_message。消息。text  =  message。文字 ;
                }

                如果（消息。附件）{
                    facebook_message。消息。附件 =  消息。附件 ;
                }

                如果（消息。sticker_id）{
                    facebook_message。消息。sticker_id  =  消息。sticker_id ;
                }

                如果（消息。quick_replies）{

                    //將標題的長度清理為最多20個字符
                    var  titleLimit  =  function（title）{
                        如果（標題。長度 >  20）{
                            var newTitle =  title。子串（0，16）+  ' ... ' ;
                            返回 newTitle;
                        } else {
                            回報標題;
                        }
                    };

                    facebook_message。消息。quick_replies  =  消息。quick_replies。map（function（item）{
                        返回 {
                            content_type ： item。content_type  ||  '文字'，
                            標題： titleLimit（項目。標題）
                            有效載荷： 項目。有效載荷，
                            image_url ： item。image_url，
                        };
                    }）;
                }
            } else {
                facebook_message。sender_action  =  消息。sender_action ;
            }

            如果（消息。sender_action）{
                facebook_message。sender_action  =  消息。sender_action ;
            }

            如果（消息。NOTIFICATION_TYPE）{
                facebook_message。notification_type  =  消息。notification_type ;
            }

            //為訪問請求添加訪問令牌
            facebook_message。access_token  =  配置。access_token ;

            請求（{
                方法： ' POST '，
                傑森： 是的，
                標題： {
                    ' content-type '： ' application / json '，
                }，
                身體： facebook_message，
                uri ： ' https : //graph.facebook.com/v2.6/me/messages '
            }，
                function（err，res，body）{


                    if（錯誤）{
                        botkit。debug（' WEBHOOK ERROR '，錯誤）;
                        return cb &&  cb（err）;
                    }

                    如果（體。錯誤）{
                        botkit。調試（' API ERROR '，體。誤差）;
                        返回 CB &&  CB（體。錯誤。消息）;
                    }

                    botkit。debug（' WEBHOOK SUCCESS '，正文）;
                    cb &&  cb（null，body）;
                }）;
        };

        機器人。startTyping  =  function（src，cb）{
            var msg = {};
            味精。channel  =  src。通道 ;
            味精。sender_action  =  ' typing_on ' ;
            機器人。說（msg，cb）;
        };

        機器人。stopTyping  =  function（src，cb）{
            var msg = {};
            味精。channel  =  src。通道 ;
            味精。sender_action  =  ' typing_off ' ;
            機器人。說（msg，cb）;
        };

        機器人。replyWithTyping  =  function（src，resp，cb）{
            var text;

            if（typeof（resp）==  ' string '）{
                text = resp;
            } else {
                text =  resp。文字 ;
            }

            var avgWPM =  85 ;
            var avgCPM = avgWPM *  7 ;

            var typingLength =  數學。分（數學。地板（文本。長度 /（avgCPM /  60））*  1000，5000）;

            機器人。startTyping（src，function（err）{
                如果（錯）控制台。log（err）;
                setTimeout（function（）{
                    機器人。回复（src，resp，cb）;
                }，typingLength）;
            }）;

        };


        機器人。reply  =  function（src，resp，cb）{
            var msg = {};

            if（typeof（resp）==  ' string '）{
                味精。text  = resp;
            } else {
                msg = resp;
            }

            味精。channel  =  src。通道 ;

            機器人。說（msg，cb）;
        };

        機器人。findConversation  =  function（message，cb）{
            botkit。調試（' CUSTOM FIND康沃'，消息。用戶，消息。信道）;
            為（VAR噸=  0 ;噸<  botkit。任務。長度 ;噸++）{
                為（VAR ç =  0 ;ç <  botkit。任務 [T]。convos。長度 ; C ^ ++）{
                    如果（
                        botkit。任務 [t]。convos [c]。isActive（）&&
                        botkit。任務 [t]。convos [c]。source_message。user  ==  message。用戶
                    ）{
                        botkit。debug（' FOUND EXISTING CONVO！'）;
                        CB（botkit。任務。[T] convos [C]）;
                        回歸 ;
                    }
                }
            }

            cb（）;
        };
        返回機器人;
    }）;

    //設置用於接收傳出webhook和/或斜杠命令的Web路由

    facebook_botkit。createWebhookEndpoints  =  function（webserver，bot，cb）{

        facebook_botkit。日誌（
            ' **為Messenger平台提供webhook端點：'  +
            ' http：// '  +  facebook_botkit。配置。主機名 +  '：'  +  facebook_botkit。配置。port  +  ' / facebook / receive '）;
        網絡服務器。post（' / facebook / receive '，function（req，res）{
            res。發送（' ok '）;
            facebook_botkit。handleWebhookPayload（req，res，bot）;
        }）;

        網絡服務器。get（' / facebook / receive '，function（req，res）{
            如果（REQ。查詢 [ ' hub.mode ' ] ==  '訂閱'）{
                如果（REQ。查詢 [ ' hub.verify_token ' ] ==  配置。verify_token）{
                    res。發送（REQ。查詢 [ ' hub.challenge ' ]）;
                } else {
                    res。發送（' OK '）;
                }
            }
        }）;

        if（cb）{
            cb（）;
        }

        return facebook_botkit;
    };

    facebook_botkit。handleWebhookPayload  =  function（req，res，bot）{

        var obj =  req。體 ;
        如果（OBJ。進入）{
            為（VAR é =  0，E <  OBJ。條目。長度，E ++）{
                為（VAR米=  0 ; M <  OBJ。條目並[e]。消息。長度 ; M ++）{
                    var facebook_message =  obj。進入 [e]。消息傳遞 [m];
                    如果（facebook_message。消息）{
                        var message = {
                            文字： facebook_message。消息。文字，
                            用戶： facebook_message。發件人。id，
                            頻道： facebook_message。發件人。id，
                            時間戳： facebook_message。時間戳，
                            seq ： facebook_message。消息。seq，
                            is_echo ： facebook_message。消息。is_echo，
                            mid ： facebook_message。消息。中，
                            sticker_id ： facebook_message。消息。sticker_id，
                            附件： facebook_message。消息。附件，
                            quick_reply ： facebook_message。消息。quick_reply，
                            輸入： ' user_message '，
                        };

                        facebook_botkit。receiveMessage（bot，message）;
                    } 否則 如果（facebook_message。回傳）{

                        //觸發一個facebook_postback事件
                        //和正常的消息接收事件。
                        //這允許開發人員在會話中接收回發。
                        var message = {
                            文字： facebook_message。回發。有效載荷，
                            有效載荷： facebook_message。回發。有效載荷，
                            用戶： facebook_message。發件人。id，
                            頻道： facebook_message。發件人。id，
                            時間戳： facebook_message。時間戳，
                            推薦： facebook_message。回發。轉介，
                        };

                        facebook_botkit。觸發器（' facebook_postback '，[機器人，消息]）;

                        如果（facebook_botkit。配置。receive_via_postback）{
                            var message = {
                                文字： facebook_message。回發。有效載荷，
                                用戶： facebook_message。發件人。id，
                                頻道： facebook_message。發件人。id，
                                時間戳： facebook_message。時間戳，
                                輸入： ' facebook_postback '，
                                推薦： facebook_message。回發。轉介，
                            };

                            facebook_botkit。receiveMessage（bot，message）;
                        }

                    } 否則 如果（facebook_message。選擇啟用）{

                        var message = {
                            optin ： facebook_message。optin，
                            用戶： facebook_message。發件人。id，
                            頻道： facebook_message。發件人。id，
                            時間戳： facebook_message。時間戳，
                        };

                        facebook_botkit。觸發器（' facebook_optin '，[bot，message]）;
                    } 否則 如果（facebook_message。交貨）{

                        var message = {
                            發貨： facebook_message。交貨，
                            用戶： facebook_message。發件人。id，
                            頻道： facebook_message。發件人。id，
                        };

                        facebook_botkit。觸發器（' message_delivered '，[bot，message]）;
                    } 否則 如果（facebook_message。閱讀）{

                        var message = {
                            閱讀： facebook_message。讀，
                            用戶： facebook_message。發件人。id，
                            頻道： facebook_message。發件人。id，
                            時間戳： facebook_message。時間戳，
                        };

                        facebook_botkit。觸發器（' message_read '，[bot，message]）;
                    } 否則 如果（facebook_message。引薦）{
                        var message = {
                            用戶： facebook_message。發件人。id，
                            頻道： facebook_message。發件人。id，
                            時間戳： facebook_message。時間戳，
                            推薦： facebook_message。轉介，
                        };

                        facebook_botkit。觸發器（' facebook_referral '，[bot，message]）;
                    }   else {
                        facebook_botkit。log（'來自Facebook收到了一條意外的消息：'，facebook_message）;
                    }
                }
            }
        }
    };

    facebook_botkit。setupWebserver  =  function（port，cb）{

        if（！ port）{
            拋出 新 錯誤（'無法啟動沒有端口的網絡服務器'）;
        }

        var static_dir =   process。cwd（）+  ' / public ' ;

        如果（facebook_botkit。配置 &&  facebook_botkit。配置。網絡服務器 &&  facebook_botkit。配置。網絡服務器。static_dir）
            static_dir =  facebook_botkit。配置。網絡服務器。static_dir ;

        facebook_botkit。配置。port  = port;

        facebook_botkit。webserver  =  express（）;

        //驗證請求來自facebook，併中止驗證錯誤
        如果（facebook_botkit。配置。validate_requests  ===  真）{
            //在我們的接收webhook上加載驗證中間件僅用於後期路由，並捕獲它可能拋出的任何錯誤以防止進一步解析請求。
            facebook_botkit。網絡服務器。交（' / Facebook的/接收'，bodyParser。JSON（{驗證： verifyRequest}））;
            facebook_botkit。網絡服務器。use（abortOnValidationError）;
        }

        facebook_botkit。網絡服務器。使用（bodyParser。JSON（））;
        facebook_botkit。網絡服務器。使用（bodyParser。urlencoded進行（{擴展： 真 }））;
        facebook_botkit。網絡服務器。使用（表達。靜態（static_dir））;

        var server =  facebook_botkit。網絡服務器。聽（
            facebook_botkit。配置。港口，
            facebook_botkit。配置。主機名，
            function（）{
                facebook_botkit。log（' **在端口上啟動網絡服務器'  +
                    facebook_botkit。配置。港口）;
                如果（CB）{ CB（空，facebook_botkit。web服務器）; }
            }）;


        要求。交（' https://graph.facebook.com/me/subscribed_apps?access_token= '  +  配置。的access_token，
            function（err，res，body）{
                if（錯誤）{
                    facebook_botkit。log（'無法訂閱頁面消息'）;
                } else {
                    facebook_botkit。debug（'成功訂閱Facebook事件：'，正文）;
                    facebook_botkit。startTicking（）;
                }
            }）;

        return facebook_botkit;

    };

    facebook_botkit。api  = {
        ' thread_settings '： {
            問候： 功能（問候）{
                var message = {
                    ' setting_type '： '問候'，
                    '問候'： {
                        ' text '：問候
                    }
                };
                facebook_botkit。api。thread_settings。postAPI（消息）;
            }，
            delete_greeting ： function（）{
                var message = {
                    ' setting_type '： '問候'，
                };
                facebook_botkit。api。thread_settings。deleteAPI（message）;
            }，
            get_started ： function（payload）{
                var message = {
                    ' setting_type '： ' call_to_actions '，
                    ' thread_state '： ' new_thread '，
                    ' call_to_actions '：
                        [
                            {
                                '有效載荷'：有效載荷
                            }
                        ]
                };
                facebook_botkit。api。thread_settings。postAPI（消息）;
            }，
            delete_get_started ： function（）{
                var message = {
                    ' setting_type '： ' call_to_actions '，
                    ' thread_state '： ' new_thread '，
                };
                facebook_botkit。api。thread_settings。deleteAPI（message）;
            }，
            menu ： function（payload）{
                var message = {
                    ' setting_type '： ' call_to_actions '，
                    ' thread_state '： ' existing_thread '，
                    ' call_to_actions '：有效載荷
                };
                facebook_botkit。api。thread_settings。postAPI（消息）;
            }，
            delete_menu ： function（）{
                var message = {
                    ' setting_type '： ' call_to_actions '，
                    ' thread_state '： ' existing_thread '，
                };
                facebook_botkit。api。thread_settings。deleteAPI（message）;
            }，
            postAPI ： function（message）{
                要求。交（' https://graph.facebook.com/v2.6/me/thread_settings?access_token= '  +  配置。的access_token，
                    {form ： message}，
                    function（err，res，body）{
                        if（錯誤）{
                            facebook_botkit。log（'無法配置線程設置'）;
                        } else {

                            var results =  null ;
                            嘗試 {
                                results =  JSON。解析（身體）;
                            } catch（err）{
                                facebook_botkit。log（' thread_settings API調用中的錯誤：無法解析JSON '，錯誤，正文）;
                            }

                            if（results）{
                                if（結果。錯誤）{
                                    facebook_botkit。log（' thread_settings API調用中的錯誤：'，結果。錯誤。消息）;
                                } else {
                                    facebook_botkit。debug（'成功配置的線程設置'，正文）;
                                }
                            }

                        }
                    }）;
            }，
            deleteAPI ： function（message）{
                要求。刪除（' https://graph.facebook.com/v2.6/me/thread_settings?access_token= '  +  配置。的access_token，
                    {form ： message}，
                    function（err，res，body）{
                        if（錯誤）{
                            facebook_botkit。log（'無法配置線程設置'）;
                        } else {
                            facebook_botkit。debug（'成功配置的線程設置'，消息）;
                        }
                    }）;
            }
        }
    };

    //在bodyParser解析之前驗證原始請求有效負載的SHA1簽名
    //如果簽名無效，將中止解析，並將一般錯誤傳遞給響應
    function  verifyRequest（req，res，buf，encoding）{
        var expected =  req。headers [ ' x-hub-signature ' ];
        var calculated =  getSignature（buf）;
        if（expected ！== calculated）{
            拋出 新 錯誤（'傳入請求上的簽名無效'）;
        } else {
            // facebook_botkit.debug（'** X-Hub驗證成功！'）
        }
    }

    function  getSignature（buf）{
        var hmac =  crypto。createHmac（' SHA1 '，facebook_botkit。配置。app_secret）;
        hmac。更新（buf，' utf-8 '）;
        返回 ' sha1 = '  +  hmac。摘要（' hex '）;
    }

    function  abortOnValidationError（err，req，res，next）{
        if（錯誤）{
            facebook_botkit。log（' **來自請求的無效X-HUB簽名！'）;
            facebook_botkit。debug（' ** X-HUB驗證錯誤：'，錯誤）;
            res。狀態（400）。發送（{
                錯誤： '簽名無效。“
            }）;
        } else {
            next（）;
        }
    }

    return facebook_botkit;
};

模塊。exports  = Facebookbot;
