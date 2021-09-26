# to-show



   @GetMapping("/board2")
   public void board2(Model model) {
      
       try {
         StringBuilder urlBuilder = new StringBuilder("http://apis.data.go.kr/1360000/VilageFcstInfoService_2.0/getUltraSrtNcst"); /*URL*/
             urlBuilder.append("?" + URLEncoder.encode("ServiceKey","UTF-8") + "=EsrycHA3klw3oZ94zTM4E4lESeLukrHoS6EyvPg0EUZVRIfGu9UVZ16jpVWImeaF6Q7%2FQ5WYpJ737fdA0qBBag%3D%3D"); /*Service Key*/
             urlBuilder.append("&" + URLEncoder.encode("ServiceKey","UTF-8") + "=" + URLEncoder.encode("-", "UTF-8")); /*공공데이터포털에서 받은 인증키*/
             urlBuilder.append("&" + URLEncoder.encode("pageNo","UTF-8") + "=" + URLEncoder.encode("1", "UTF-8")); /*페이지번호*/
             urlBuilder.append("&" + URLEncoder.encode("numOfRows","UTF-8") + "=" + URLEncoder.encode("10", "UTF-8")); /*한 페이지 결과 수*/
             urlBuilder.append("&" + URLEncoder.encode("dataType","UTF-8") + "=" + URLEncoder.encode("XML", "UTF-8")); /*요청자료형식(XML/JSON) Default: XML*/
             urlBuilder.append("&" + URLEncoder.encode("base_date","UTF-8") + "=" + URLEncoder.encode("20210926", "UTF-8")); /*‘21년 6월 28일 발표*/
             urlBuilder.append("&" + URLEncoder.encode("base_time","UTF-8") + "=" + URLEncoder.encode("0600", "UTF-8")); /*06시 발표(정시단위) */
             urlBuilder.append("&" + URLEncoder.encode("nx","UTF-8") + "=" + URLEncoder.encode("55", "UTF-8")); /*예보지점의 X 좌표값*/
             urlBuilder.append("&" + URLEncoder.encode("ny","UTF-8") + "=" + URLEncoder.encode("127", "UTF-8")); /*예보지점의 Y 좌표값*/
             URL url = new URL(urlBuilder.toString());
             HttpURLConnection conn = (HttpURLConnection) url.openConnection();
             conn.setRequestMethod("GET");
             conn.setRequestProperty("Content-type", "application/json");
             System.out.println("Response code: " + conn.getResponseCode());
             BufferedReader rd;
             if(conn.getResponseCode() >= 200 && conn.getResponseCode() <= 300) {
                 rd = new BufferedReader(new InputStreamReader(conn.getInputStream()));
             } else {
                 rd = new BufferedReader(new InputStreamReader(conn.getErrorStream()));
             }
             StringBuilder sb = new StringBuilder();
             String line;
             while ((line = rd.readLine()) != null) {
                 sb.append(line);
             }
             rd.close();
             conn.disconnect();
             System.out.println(sb.toString());
             
             JSONParser jsonParser = new JSONParser();
            JSONObject jsonObj = (JSONObject) jsonParser.parse(result);
            JSONObject parse_response = (JSONObject) jsonObj.get("response");
            JSONObject parse_body = (JSONObject) parse_response.get("body");// response 로 부터 body 찾아오기
            JSONObject parse_items = (JSONObject) parse_body.get("items");// body 로 부터 items 받아오기
            // items로 부터 itemlist 를 받아오기 itemlist : 뒤에 [ 로 시작하므로 jsonarray이다.
            JSONArray parse_item = (JSONArray) parse_items.get("item");
                                                         
            JSONObject obj;
            String category; // 기준 날짜와 기준시간을 VillageWeather 객체에 저장합니다.
            
            String day = "";
            String time = "";
            
            List<VillageWeather> datalist = new ArrayList<VillageWeather>();
            for (int i = 0; i < parse_item.size(); i++) {
               obj = (JSONObject) parse_item.get(i); // 해당 item을 가져옵니다.

               Object fcstValue = obj.get("fcstValue");
               Object fcstDate = obj.get("fcstDate");
               Object fcstTime = obj.get("fcstTime");
               category = (String) obj.get("category"); // item에서 카테고리를 검색해옵니다.
               // 검색한 카테고리와 일치하는 변수에 문자형으로 데이터를 저장합니다.
               // 데이터들이 형태가 달라 문자열로 통일해야 편합니다. 꺼내서 사용할때 다시변환하는게 좋습니다.
               
               
               switch (category) {
               case "REH":
                  vl.reh = (obj.get("fcstValue")).toString();
                  break;
               case "T3H":
                  vl.t3h = (obj.get("fcstValue")).toString();
                  break;
               }
               if (!day.equals(fcstDate.toString())) {
                  day = fcstDate.toString();
               }
               if (!time.equals(fcstTime.toString())) {
                  time = fcstTime.toString();
                  System.out.println("데이: "+ day + "  " +"타임: "+ time);
               }      
               
               vl.baseDate = (obj.get("fcstDate")).toString();
               vl.baseTime = (obj.get("fcstTime")).toString();
               
               VillageWeather test = new VillageWeather();
               test.setT3h(vl.getT3h());
               test.setReh(vl.getReh());
               test.setBaseDate(vl.getBaseDate());
               test.setBaseTime(vl.getBaseTime());
               
               
               System.out.print("\tcategory : " + category);
               System.out.print(", fcst_Value : " + fcstValue);
               System.out.print(", fcstDate : " + vl.baseDate);
               System.out.println(", fcstTime : " + vl.baseTime);      

               
               //test = vl;
               if(category.equals("T3H")) {
                  System.out.println("HAHA!");
                  datalist.add(test);
               }else {
                  System.out.println("ㅜㅜ");
               }
               
               
            }   

            
            return datalist;// 모든값이 저장된 VillageWeather객체를 반환합니다.
             
             
             model.addAttribute("sb", sb);
      } catch (UnsupportedEncodingException e) {
         // TODO Auto-generated catch block
         e.printStackTrace();
      } catch (MalformedURLException e) {
         // TODO Auto-generated catch block
         e.printStackTrace();
      } catch (ProtocolException e) {
         // TODO Auto-generated catch block
         e.printStackTrace();
      } catch (IOException e) {
         // TODO Auto-generated catch block
         e.printStackTrace();
      }

   }
   
