
# Technicalisto

## Convert .qt to .mp4 video when using multipartFormData to upload with Alamofire

1. When you try to upload to alamofire by this line but upload as .qt :


         multipartFormData.append(data , withName: "video", fileName: "video.mp4" , mimeType: ".mp4")
    

2. You can add this extension to export as .mp4 : 

         extension AVURLAsset { // it is copied from Stackoverflow

         func exportVideo(presetName: String = AVAssetExportPresetHighestQuality, outputFileType: AVFileType = .mp4, fileExtension: String = "mp4", then completion: @escaping (URL?) -> Void)
           {
            let filename = url.deletingPathExtension().appendingPathExtension(fileExtension).lastPathComponent
            let outputURL = FileManager.default.temporaryDirectory.appendingPathComponent(filename)

        if let session = AVAssetExportSession(asset: self, presetName: presetName) {
            session.outputURL = outputURL
            session.outputFileType = outputFileType
            let start = CMTimeMakeWithSeconds(0.0, preferredTimescale: 0)
            let range = CMTimeRangeMake(start: start, duration: duration)
            session.timeRange = range
            session.shouldOptimizeForNetworkUse = true
            session.exportAsynchronously {
                switch session.status {
                case .completed:
                    completion(outputURL)
                case .cancelled:
                    debugPrint("Video export cancelled.")
                    completion(nil)
                case .failed:
                    let errorMessage = session.error?.localizedDescription ?? "n/a"
                    debugPrint("Video export failed with error: \(errorMessage)")
                    completion(nil)
                default:
                    break
                }
            }
        } else {
            completion(nil)
         }
         }
     }
     

3. And using it like :

        if let mediaType = info[UIImagePickerController.InfoKey.mediaType] as? String {
        
        if mediaType == "public.movie" {

        let avAsset = AVURLAsset(url: videoURL as! URL, options: nil)
        
        avAsset.exportVideo(presetName: AVAssetExportPresetHighestQuality, outputFileType: AVFileType.mp4, fileExtension: "mp4") { (mp4Url) in
        
        print("Mp4 converted url : \(String(describing: mp4Url))")
        
        self.videoURl = mp4Url
        
        }} }
        

### Thanks

