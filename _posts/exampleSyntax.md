
## start of code

```swift
//
//  AppStoreSearchPickerView.swift
//  MenuBox
//
//  Created by Alex Hay on 02/12/2020.
//

import Foundation
import SwiftUI

struct AppStoreSearchPickerView: View {
	
	@Environment(\.colorScheme) var colorScheme
	@Binding var showingModal: ActiveModal?
	@State var navTitle: String
	@Binding var image: UIImage?
	@State var searchQuery = ""
	@State var searching = false
	@State var results: [AppResult] = []
	@State var errorMessage = ""
	@State var showingError = false
	
	var body: some View {
		NavigationView {
			List {
				SearchBarView(searchQuery: $searchQuery, isLowercase: true)
					.listRowBackground(self.colorScheme == .light ? Color(.systemGray6) : Color(.systemBackground))
					.padding(.horizontal)
					.padding(.top)
					if showingError {
						Section {
							VStack(alignment: .center) {
								Text("\(Image(systemName: "exclamationmark.triangle")) An Error Occurred")
									.font(.headline)
									.fontWeight(.bold)
									.padding(.bottom)
								Text(errorMessage)
									.font(.caption)
									.foregroundColor(.secondary)
							}
						}
					} else {
						Section {
							if searching {
								HStack {
									Spacer()
									ProgressView()
									Spacer()
								}
							} else {
								ForEach(results, id: \.id) { app in
									Button {
										Vibration.selection.vibrate()
										getAppIcon(from: app.iconURL) { data, urlResponse, error in
											if let imageData = data, let appIcon = UIImage(data: imageData) {
												image = appIcon
												showingModal = .imageMaskPicker
											} else {
												Vibration.error.vibrate()
												if error != nil {
													errorMessage = "Couldn't download app icon: \(error?.localizedDescription ?? "Unknown Error")"
												} else {
													errorMessage = "Couldn't download app icon. Please check your connection and try again"
												}
												showingError = true
												searching = false
											}
										}
									} label: {
										HStack {
											RemoteImage(imageUrl: app.thumbURL.absoluteString, title: "Thumbnail", width: 35, height: 35, cornerRadius: 0)
												.frame(width: 35, height: 35, alignment: .center)
												.cornerRadius(5)
												.padding(3)
											Text(app.name)
											Spacer()
										}
										.contextMenu {
											Button {
												Vibration.success.vibrate()
												UIPasteboard.general.string = app.appStoreURL.absoluteString
											} label: {
												HStack {
													Text("Copy App Store URL")
													Spacer()
													Image(systemName: "doc.on.clipboard")
												}
											}
											Button {
												Vibration.medium.vibrate()
												UIApplication.shared.open(app.appStoreURL)
											} label: {
												HStack {
													Text("Open In App Store")
													Spacer()
													Image(systemName: "link")
												}
											}
											Button {
												Vibration.success.vibrate()
												UIPasteboard.general.string = app.bundleID
											} label: {
												HStack {
													Text("Copy Bundle ID")
													Spacer()
													Image(systemName: "doc.on.clipboard")
												}
											}
										}
									}
									.foregroundColor(.primary)
								}
							}
						}
					}
			}
			.onChange(of: searchQuery) { value in
				searchAppStore()
			}
			.navigationTitle(navTitle)
			.listStyle(InsetGroupedListStyle())
			.toolbar {
				ToolbarItem(placement: .cancellationAction) {
					Button {
						showingModal = nil
					} label: {
						Text("Cancel")
					}
				}
			}
		}
	}
	
	func getAppIcon(from url: URL, completion: @escaping (Data?, URLResponse?, Error?) -> ()) {
		URLSession.shared.dataTask(with: url, completionHandler: completion).resume()
	}
	
	func searchAppStore() {
		showingError = false
		searching = true
		let locale = Locale.current
		let countryCode = (locale as NSLocale).object(forKey: .countryCode) as? String ?? "US"
		
		// Build query URL
		var components = URLComponents()
		components.scheme = "https"
		components.host = "itunes.apple.com"
		components.path = "/search/"
		
		let queryItems: [URLQueryItem] = [
											URLQueryItem(name: "entity", value: "software"),
											URLQueryItem(name: "term", value: searchQuery.replacingOccurrences(of: " ", with: "+").addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed)),
											URLQueryItem(name: "country", value: countryCode),
											URLQueryItem(name: "media", value: "software"),
											URLQueryItem(name: "limit", value: "25")
											]
		components.queryItems = queryItems
		guard let url = components.url else {
			errorMessage = "Couldn't build from components"
			showingError = true
			searching = false
			return
		}
		let request = URLRequest(url: url)
		
		URLSession.shared.dataTask(with: request) { data, response, error in
			if let data = data {
				do {
					// print(data.prettyPrintedJSONString ?? "")
					let decodedResponse = try JSONDecoder().decode(Response.self, from: data)
					// we have good data – go back to the main thread
					DispatchQueue.main.async {
						showingError = false
						searching = false
						results = decodedResponse.results
					}
				} catch let decodingError {
					debugPrint(decodingError)
					errorMessage = "Couldn't decode search results: \(decodingError.localizedDescription)"
					showingError = true
					searching = false
					return
				}
				
			} else {
				errorMessage = "Couldn't get results: \(error?.localizedDescription ?? "Unknown error")"
				showingError = true
				searching = false
				return
			}
		}.resume()
	}
}

struct Response: Codable {
	var resultCount: Int
	var results: [AppResult]
}

struct AppResult: Codable {
	var id: Int
	var thumbURL: URL
	var iconURL: URL
	var developer: String
	var name: String
	var bundleID: String
	var appStoreURL: URL
}

extension AppResult {
	enum CodingKeys: String, CodingKey {
		case id = "trackId"
		case thumbURL = "artworkUrl60"
		case iconURL = "artworkUrl512"
		case developer = "artistName"
		case name = "trackName"
		case bundleID = "bundleId"
		case appStoreURL = "trackViewUrl"
	}
}
```

## end of code
