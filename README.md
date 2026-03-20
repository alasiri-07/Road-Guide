<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>دليل الطرق الفاخر</title>
    
    <!-- Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@300;400;600;700;800&display=swap" rel="stylesheet">
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { sans: ['Cairo', 'sans-serif'] },
                    colors: { primary: '#0f4c5c', secondary: '#e3b23c' }
                }
            }
        }
    </script>

    <!-- React & ReactDOM -->
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    
    <!-- Babel -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    
    <!-- PapaParse لقراءة ملفات الإكسل بكفاءة -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
    
    <!-- SheetJS لقراءة ملفات الاكسل -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>

    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <style>
        body { background-color: #f8fafc; overflow-x: hidden; }
        .glass-panel {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 10px 40px -10px rgba(0,0,0,0.08);
        }
        .smooth-transition { transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .hide-scrollbar::-webkit-scrollbar { display: none; }
        .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useMemo } = React;

        // الصق محتوى ملف CSV كاملاً هنا بين علامتي ` `
        const initialData = `
,1,404,15,مكة المكرمة / المدينة المنورة السريع,سريع,193.664,1,1,1541+200,1734+864,39.787915,22.779141,39.563396,24.286403,https://maps.google.com/?q=22.779141,39.787915,https://maps.google.com/?q=24.286403,39.563396,404
,2,404,4377,طريق حجر / المواريد,مفرد,3.4,2,5,0+000,3+400,39.9037,22.9385,39.8842,22.9191,https://maps.google.com/?q=22.9385,39.9037,https://maps.google.com/?q=22.9191,39.8842,404
,82,404,4391,صفينة - الصلحانية - حازاه,مفرد,57.4157,3,3,0+000,57+416,40.502069,23.121297,40.602171,22.629804,https://maps.google.com/?q=23.121297,40.502069,https://maps.google.com/?q=22.629804,40.602171,404
`.trim();

        const App = () => {
            const [data, setData] = useState([]);
            const [query, setQuery] = useState('');
            const [selectedRoad, setSelectedRoad] = useState(null);
            const [notification, setNotification] = useState(null);

            const showNotification = (msg, type) => {
                setNotification({ msg, type });
                setTimeout(() => setNotification(null), 4000);
            };

            // قراءة البيانات المدمجة مباشرة
            const parseData = (input) => {
                Papa.parse(input, {
                    skipEmptyLines: true,
                    complete: function(results) {
                        const parsedData = [];
                        results.data.forEach(cols => {
                            if (cols[3] && String(cols[3]).trim() !== '' && String(cols[3]).trim() !== 'رقم الطريق' && !String(cols[3]).includes('الطريق')) {
                                parsedData.push({
                                    id: Math.random().toString(36).substr(2, 9),
                                    contractNumber: cols[2]?.trim(), // C
                                    roadNumber: cols[3]?.trim(), // D
                                    roadName: cols[4]?.trim()?.replace(/['"]/g, ''), // E
                                    roadType: cols[5]?.trim(), // F
                                    length: cols[6]?.trim(), // G
                                    currentServiceLevel: cols[7]?.trim(), // H
                                    startKm: cols[9]?.trim(), // J
                                    endKm: cols[10]?.trim(), // K
                                    startLong: cols[11]?.trim(), // L
                                    startLat: cols[12]?.trim(), // M
                                    endLong: cols[13]?.trim(), // N
                                    endLat: cols[14]?.trim(), // O
                                    startLink: cols[15]?.trim()?.replace(/['"]/g, ''), // P
                                    endLink: cols[16]?.trim()?.replace(/['"]/g, '') // Q
                                });
                            }
                        });
                        setData(parsedData);
                    }
                });
            };

            useEffect(() => {
                parseData(initialData);
                lucide.createIcons();
            }, []);

            useEffect(() => {
                lucide.createIcons();
            });

            const filteredRoads = useMemo(() => {
                if (!query) return [];
                const cleanQuery = query.trim().toLowerCase();
                return data.filter(road => {
                    const numMatch = String(road.roadNumber || '').toLowerCase().includes(cleanQuery);
                    const nameMatch = String(road.roadName || '').toLowerCase().includes(cleanQuery);
                    return numMatch || nameMatch;
                }).slice(0, 15);
            }, [query, data]);

            return (
                <div className="min-h-screen flex flex-col items-center py-10 px-4 sm:px-6 lg:px-8 relative">
                    <div className="absolute top-0 left-0 w-full h-96 bg-primary rounded-b-[4rem] -z-10 shadow-2xl"></div>
                    <div className="absolute top-10 left-10 w-32 h-32 bg-secondary opacity-20 rounded-full blur-3xl"></div>
                    <div className="absolute top-20 right-20 w-48 h-48 bg-teal-400 opacity-20 rounded-full blur-3xl"></div>

                    <div className="text-center mt-8 mb-12">
                        <h1 className="text-4xl md:text-5xl font-extrabold text-white mb-4 tracking-tight">دليـــــل الطــــــرق</h1>
                        <p className="text-white/80 font-medium">إجمالي الطرق المضافة: {data.length}</p>
                    </div>

                    {!selectedRoad ? (
                        <div className="w-full max-w-2xl relative z-10">
                            <div className="relative group">
                                <div className="absolute inset-y-0 right-0 pr-4 flex items-center pointer-events-none text-gray-400">
                                    <i data-lucide="search" className="w-6 h-6"></i>
                                </div>
                                <input
                                    type="text"
                                    className="w-full glass-panel text-gray-900 placeholder-gray-400 rounded-2xl py-5 pr-12 pl-4 text-lg focus:outline-none focus:ring-4 focus:ring-secondary/50 smooth-transition"
                                    placeholder="ابحث برقم الطريق أو اسمه..."
                                    value={query}
                                    onChange={(e) => setQuery(e.target.value)}
                                />
                            </div>

                            {query && (
                                <div className="mt-4 glass-panel rounded-2xl overflow-hidden max-h-96 overflow-y-auto hide-scrollbar">
                                    {filteredRoads.length > 0 ? (
                                        <ul className="divide-y divide-gray-100">
                                            {filteredRoads.map((road, idx) => (
                                                <li key={idx}>
                                                    <button 
                                                        onClick={() => setSelectedRoad(road)}
                                                        className="w-full text-right px-6 py-4 hover:bg-primary/5 smooth-transition flex items-center justify-between group"
                                                    >
                                                        <div>
                                                            <p className="text-lg font-bold text-gray-800 group-hover:text-primary">{road.roadName || 'بدون اسم'}</p>
                                                            <p className="text-sm text-gray-500 mt-1 flex items-center gap-1">
                                                                <i data-lucide="hash" className="w-3 h-3"></i> رقم الطريق: {road.roadNumber}
                                                            </p>
                                                        </div>
                                                        <i data-lucide="chevron-left" className="w-5 h-5 text-gray-400 group-hover:text-secondary"></i>
                                                    </button>
                                                </li>
                                            ))}
                                        </ul>
                                    ) : (
                                        <div className="px-6 py-8 text-center text-gray-500">
                                            <i data-lucide="map-x" className="w-12 h-12 mx-auto mb-3 text-gray-300"></i>
                                            <p>لا يوجد نتائج.</p>
                                        </div>
                                    )}
                                </div>
                            )}
                        </div>
                    ) : (
                        <div className="w-full max-w-4xl glass-panel rounded-3xl p-8 animate-fade-in-up relative">
                            <button 
                                onClick={() => setSelectedRoad(null)}
                                className="absolute top-6 left-6 flex items-center justify-center w-10 h-10 rounded-full bg-gray-100 hover:bg-gray-200 text-gray-600 smooth-transition"
                            >
                                <i data-lucide="x" className="w-5 h-5"></i>
                            </button>

                            <div className="mb-8 border-b border-gray-100 pb-6">
                                <span className="inline-block px-3 py-1 bg-primary/10 text-primary text-sm font-bold rounded-full mb-3">
                                    طريق {selectedRoad.roadType || 'غير محدد'}
                                </span>
                                <h2 className="text-3xl font-extrabold text-gray-900">{selectedRoad.roadName || 'بدون اسم'}</h2>
                                <p className="text-gray-500 mt-2 font-medium flex items-center gap-2">
                                    <i data-lucide="hash" className="w-4 h-4"></i> رقم الطريق: {selectedRoad.roadNumber}
                                </p>
                            </div>

                            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                                <DetailItem icon="file-text" label="رقم العقد" value={selectedRoad.contractNumber} />
                                <DetailItem icon="ruler" label="الطول" value={`${selectedRoad.length} كلم`} />
                                <DetailItem icon="activity" label="مستوى الخدمة" value={selectedRoad.currentServiceLevel} />
                                <DetailItem icon="git-commit" label="العلامة الكيلومترية" value={`من ${selectedRoad.startKm} إلى ${selectedRoad.endKm}`} />

                                <div className="md:col-span-2 mt-4 space-y-4">
                                    <CoordinateCard title="البداية" lon={selectedRoad.startLong} lat={selectedRoad.startLat} link={selectedRoad.startLink} icon="map-pin" color="text-emerald-600" bg="bg-emerald-50" />
                                    <CoordinateCard title="النهاية" lon={selectedRoad.endLong} lat={selectedRoad.endLat} link={selectedRoad.endLink} icon="flag" color="text-rose-600" bg="bg-rose-50" />
                                </div>
                            </div>
                        </div>
                    )}
                </div>
            );
        };

        const DetailItem = ({ icon, label, value }) => (
            <div className="flex items-start gap-4 p-4 rounded-2xl bg-gray-50/50">
                <div className="w-10 h-10 rounded-full bg-white flex items-center justify-center text-primary shrink-0"><i data-lucide={icon} className="w-5 h-5"></i></div>
                <div><p className="text-sm text-gray-500 font-semibold mb-1">{label}</p><p className="text-lg font-bold text-gray-900">{value || '-'}</p></div>
            </div>
        );

        const CoordinateCard = ({ title, lon, lat, link, icon, color, bg }) => (
            <div className={`flex flex-col sm:flex-row items-center justify-between p-5 rounded-2xl ${bg}`}>
                <div className="flex items-center gap-4">
                    <div className={`w-12 h-12 rounded-full bg-white flex items-center justify-center ${color} shrink-0`}><i data-lucide={icon} className="w-6 h-6"></i></div>
                    <div><p className="text-sm font-bold text-gray-800">{title}</p><p className="text-base font-mono text-gray-600" dir="ltr">{lon && lat ? `${lon} ${lat}` : 'غير متوفر'}</p></div>
                </div>
                {link && <a href={link} target="_blank" className="bg-white px-5 py-2.5 rounded-xl font-bold text-gray-800 flex items-center gap-2"><span>الخريطة</span><i data-lucide="external-link" className="w-4 h-4"></i></a>}
            </div>
        );

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
