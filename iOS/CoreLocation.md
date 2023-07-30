~~~
import UIKit
import CoreLocation

class WeatherViewController: UIViewController, CLLocationManagerDelegate {

    let locationManager = CLLocationManager()

    override func viewDidLoad() {
    
        super.viewDidLoad()
        
        locationManager.requestAlwaysAuthorization()
        locationManager.requestWhenInUseAuthorization()

        DispatchQueue.global().async {
            if CLLocationManager.locationServicesEnabled() {
                self.locationManager.delegate = self
                self.locationManager.desiredAccuracy = kCLLocationAccuracyNearestTenMeters
                self.locationManager.startUpdatingLocation()
            }
        }
        
    }


    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {

        guard let locValue: CLLocationCoordinate2D = manager.location?.coordinate else { return }

        print("locations = \(locValue.latitude) \(locValue.longitude)")

    }

    func locationManager(_ manager: CLLocationManager, didFailWithError error: Error) {

        print(error)

    }

}
~~~


위치 관련 정보를 얻기 전에 requestAlwaysAuthorization()이나 requsetWhenInUseAuthorization()은 꼭 호출해야한다.
앱을 사용하지 않을 때에도 위치정보를 확인할 수 있게 하는것과 사용할 때만 사용할 수 있는 메소드다.

### requestAlwaysAuthorization()
### requestWhenInUseAuthorization()



### locationServicesEnabled()

해당 메소드는 위치정보를 사용가능하면 true를 내뱉는데,
어떤것 때문인지는 정확히 모르겠으나, 기다리는동안 UI작업이 멈출 수 있어서 글로벌큐로 보내서 작업하는것이 좋다고한다.

