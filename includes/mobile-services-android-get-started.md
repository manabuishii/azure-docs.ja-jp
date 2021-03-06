このチュートリアルの最後に、新しいアプリケーションをビルドして実行します。

### プロジェクトを Android Studio に読み込んで、Gradle を同期する
1. 圧縮されたプロジェクト ファイルの保存場所を参照し、ファイルをコンピューター上の Android Studio プロジェクトのディレクトリに展開します。
2. Android Studio を起動します。プロジェクトを使用していて、それが表示された場合は、そのプロジェクトを閉じます ([ファイル] => [プロジェクトを閉じる])。
3. **[既存の Android Studio プロジェクトを開く]** を選択し、プロジェクトの場所を参照して、**[OK]** をクリックします。 これにより、プロジェクトが読み込まれ、Gradle との同期が開始されます。
   
     ![](./media/mobile-services-android-get-started/android-studio-import-project.png)
4. Gradle 同期アクティビティが完了するを待ちます。"ターゲットを見つけられませんでした" というエラーが発生した場合、サンプルのバージョンが Android Studio で使用されるバージョンと一致していないことが原因です。エラー メッセージ内の **[不足しているプラットフォームをインストールして、プロジェクトを同期する]** というリンクをクリックすれば、最も簡単に問題を修正できます。別のバージョンのエラー メッセージが表示される場合がありますが、エラーが表示されなくなるまでこのプロセスを繰り返してください。
   
   * Android の最新バージョンや最上位バージョンで実行する場合は、別の修正方法があります。*app* ディレクトリにある *build.gradle* 内の **targetSdkVersion** を、コンピューターにインストールされているバージョンと一致するように更新します。このバージョンを確認するには、**SDK Manager** アイコンをクリックします。次に、**[Sync Project with Gradle Files]** をクリックします。ビルド ツールのバージョンに関するエラー メッセージが表示される場合がありますが、同じ方法で修正してください。

### アプリの実行
エミュレーターまたは実際のデバイスでアプリを実行できます。

1. デバイスから実行するには、USB ケーブルでコンピューターに電話を接続します。[開発用にデバイスをセットアップする](https://developer.android.com/training/basics/firstapp/running-app.html)必要があります。Windows コンピューターで開発している場合は、USB ドライバーもダウンロードしてインストールする必要があります。
2. Android エミュレーターを使用して実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager アイコンクリックします。
3. **[実行]** メニューの **[実行]** をクリックしてプロジェクトを開始し、表示されたダイアログ ボックスでデバイスまたはエミュレーターを選択します。
4. アプリが表示されたら、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[追加]** をクリックします。
   
       ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)
   
       これで、Azure でホストされている新しいモバイル サービスに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル サービスによって返され、データが一覧に表示されます。
   
   > [!NOTE]
   > モバイル サービスにアクセスして ToDoActivity.java ファイルにあるデータをクエリ会および挿入するコードを確認できます。
   > 
   > 
5. Azure クラシック ポータルに戻り、**[データ]** タブ、**TodoItems** テーブルの順にクリックします。
   
       ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)
   
       これで、アプリケーションによってテーブルに挿入されたデータを参照できます。
   
       ![](./media/mobile-services-android-get-started/mobile-data-browse.png)

<!---HONumber=AcomDC_1203_2015-->